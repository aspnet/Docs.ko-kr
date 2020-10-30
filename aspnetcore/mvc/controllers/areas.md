---
title: ASP.NET Core의 영역
author: rick-anderson
description: 관련 기능을 별도의 네임스페이스(라우팅용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 ASP.NET MVC 기능인 영역에 대해 알아봅니다.
ms.author: riande
ms.date: 03/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053529"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="65edf-103">ASP.NET Core의 영역</span><span class="sxs-lookup"><span data-stu-id="65edf-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="65edf-104">[Dhananjay Kumar](https://twitter.com/debug_mode) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="65edf-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65edf-105">영역은 별도의 그룹으로 관련 기능을 구성 하는 데 사용 되는 ASP.NET 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="65edf-106">라우팅에 대 한 네임 스페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-106">Namespace for routing.</span></span>
* <span data-ttu-id="65edf-107">보기 및 페이지에 대 한 폴더 구조 :::no-loc(Razor)::: 입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-107">Folder structure for views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="65edf-108">영역을 사용 하면 다른 경로 매개 변수, `area` 를 및 또는 페이지에 추가 하 여 라우팅의 용도에 대 한 계층을 만들 수 `controller` `action` :::no-loc(Razor)::: `page` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="65edf-109">영역을 사용 하 여 ASP.NET Core 웹 앱을 더 작은 기능 그룹으로 분할 하 고 각각 자체 :::no-loc(Razor)::: 페이지, 컨트롤러, 뷰 및 모델 집합을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="65edf-110">영역은 사실상 앱 내부의 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="65edf-111">ASP.NET Core 웹 프로젝트에서 페이지, 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 보관됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="65edf-112">ASP.NET Core 런타임은 명명 규칙을 사용하여 이러한 구성 요소 간의 관계를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="65edf-113">대형 앱의 경우 앱을 별도의 고급 기능 영역으로 나누는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="65edf-114">결제, 청구 및 검색과 같은 여러 비즈니스 단위가 있는 전자상거래 앱을 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="65edf-115">이러한 각 단위에는 보기, 컨트롤러, 페이지 및 모델을 포함 하는 고유한 영역이 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-115">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="65edf-116">다음과 같은 경우 프로젝트에서 영역을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="65edf-117">앱은 논리적으로 분리할 수 있는 여러 개의 고급 기능 구성 요소로 이루어져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="65edf-118">각 기능 영역을 독립적으로 작업할 수 있도록 앱을 나누고자 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="65edf-119">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="65edf-120">다운로드 샘플은 테스트 영역에 대한 기본 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="65edf-121">페이지를 사용 하 :::no-loc(Razor)::: 는 경우이 문서의 [ :::no-loc(Razor)::: 페이지를 포함](#areas-with-razor-pages) 하는 영역을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-121">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="65edf-122">보기가 있는 컨트롤러 영역</span><span class="sxs-lookup"><span data-stu-id="65edf-122">Areas for controllers with views</span></span>

<span data-ttu-id="65edf-123">영역, 컨트롤러 및 보기를 사용하는 일반적인 ASP.NET Core 웹앱은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="65edf-124">[영역 폴더 구조](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="65edf-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="65edf-125">[`[Area]`](#attribute)컨트롤러를 영역에 연결 하는 특성을 가진 컨트롤러:</span><span class="sxs-lookup"><span data-stu-id="65edf-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="65edf-126">[시작에 추가 된 영역 경로](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="65edf-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="65edf-127">영역 폴더 구조</span><span class="sxs-lookup"><span data-stu-id="65edf-127">Area folder structure</span></span>

<span data-ttu-id="65edf-128">*Products* 및 *Services* 의 두 논리 그룹이 존재하는 앱을 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="65edf-129">영역을 사용할 경우 폴더 구조는 다음과 유사할 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="65edf-130">프로젝트 이름</span><span class="sxs-lookup"><span data-stu-id="65edf-130">Project name</span></span>
  * <span data-ttu-id="65edf-131">Areas</span><span class="sxs-lookup"><span data-stu-id="65edf-131">Areas</span></span>
    * <span data-ttu-id="65edf-132">제품</span><span class="sxs-lookup"><span data-stu-id="65edf-132">Products</span></span>
      * <span data-ttu-id="65edf-133">Controllers</span><span class="sxs-lookup"><span data-stu-id="65edf-133">Controllers</span></span>
        * <span data-ttu-id="65edf-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="65edf-134">HomeController.cs</span></span>
        * <span data-ttu-id="65edf-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="65edf-135">ManageController.cs</span></span>
      * <span data-ttu-id="65edf-136">보기</span><span class="sxs-lookup"><span data-stu-id="65edf-136">Views</span></span>
        * <span data-ttu-id="65edf-137">홈</span><span class="sxs-lookup"><span data-stu-id="65edf-137">Home</span></span>
          * <span data-ttu-id="65edf-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-138">Index.cshtml</span></span>
        * <span data-ttu-id="65edf-139">관리</span><span class="sxs-lookup"><span data-stu-id="65edf-139">Manage</span></span>
          * <span data-ttu-id="65edf-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-140">Index.cshtml</span></span>
          * <span data-ttu-id="65edf-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-141">About.cshtml</span></span>
    * <span data-ttu-id="65edf-142">서비스</span><span class="sxs-lookup"><span data-stu-id="65edf-142">Services</span></span>
      * <span data-ttu-id="65edf-143">Controllers</span><span class="sxs-lookup"><span data-stu-id="65edf-143">Controllers</span></span>
        * <span data-ttu-id="65edf-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="65edf-144">HomeController.cs</span></span>
      * <span data-ttu-id="65edf-145">보기</span><span class="sxs-lookup"><span data-stu-id="65edf-145">Views</span></span>
        * <span data-ttu-id="65edf-146">홈</span><span class="sxs-lookup"><span data-stu-id="65edf-146">Home</span></span>
          * <span data-ttu-id="65edf-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-147">Index.cshtml</span></span>

<span data-ttu-id="65edf-148">영역을 사용할 때는 이전 레이아웃이 일반적인 반면, 이 폴더 구조를 사용하기 위해서는 보기 파일만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="65edf-149">보기 검색은 일치하는 영역 보기 파일을 다음 순서로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="65edf-150">컨트롤러를 영역과 연결</span><span class="sxs-lookup"><span data-stu-id="65edf-150">Associate the controller with an Area</span></span>

<span data-ttu-id="65edf-151">영역 컨트롤러는 [ &lbrack; 영역 &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성으로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="65edf-152">영역 경로 추가</span><span class="sxs-lookup"><span data-stu-id="65edf-152">Add Area route</span></span>

<span data-ttu-id="65edf-153">일반적으로 영역 경로는 [특성 라우팅이](xref:mvc/controllers/routing#ar)아닌 [기존 라우팅을](xref:mvc/controllers/routing#cr) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="65edf-154">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="65edf-155">일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="65edf-156">모든 영역에서 url 공간이 동일한 경우 `{area:...}`를 경로 템플릿의 토큰으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="65edf-157">이전 코드에서 `exists`는 경로가 영역과 일치해야 한다는 제약 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="65edf-158">사용 `{area:...}` `MapControllerRoute` 위치:</span><span class="sxs-lookup"><span data-stu-id="65edf-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="65edf-159">는 영역에 라우팅을 추가 하는 가장 덜 복잡 한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="65edf-160">특성과 모든 컨트롤러를 일치 시킵니다 `[Area("Area name")]` .</span><span class="sxs-lookup"><span data-stu-id="65edf-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="65edf-161">다음 코드는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>를 사용하여 명명된 두 개의 영역 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="65edf-162">자세한 내용은 [영역 라우팅](xref:mvc/controllers/routing#areas)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="65edf-163">MVC 영역과 링크 생성</span><span class="sxs-lookup"><span data-stu-id="65edf-163">Link generation with MVC areas</span></span>

<span data-ttu-id="65edf-164">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)의 다음 코드는 지정된 영역과 링크 생성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="65edf-165">샘플 다운로드에는 다음이 포함 된 [부분 보기가](xref:mvc/views/partial) 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="65edf-166">이전 링크입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-166">The preceding links.</span></span>
* <span data-ttu-id="65edf-167">위의 except와 유사한 링크 `area` 는 지정 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="65edf-168">이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="65edf-169">영역을 지정하지 않고 생성된 링크는 동일한 영역 및 컨트롤러 페이지에서 참조할 때만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="65edf-170">영역 또는 컨트롤러를 지정하지 않으면 라우팅은 [앰비언트](xref:mvc/controllers/routing#ambient) 값에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="65edf-171">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="65edf-172">샘플 앱에 대 한 대부분의 경우 앰비언트 값을 사용 하면 영역을 지정 하지 않는 태그를 사용 하 여 잘못 된 링크가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="65edf-173">자세한 정보는 [컨트롤러 작업에 라우팅](xref:mvc/controllers/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="65edf-174">_ViewStart.cshtml 파일을 사용하여 영역에 대한 레이아웃 공유</span><span class="sxs-lookup"><span data-stu-id="65edf-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="65edf-175">전체 앱에 대 한 공통 레이아웃을 공유 하려면 [응용 프로그램 루트 폴더](#arf)에 *_ViewStart* 를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="65edf-176">자세한 내용은 <xref:mvc/views/layout>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="65edf-177">응용 프로그램 루트 폴더</span><span class="sxs-lookup"><span data-stu-id="65edf-177">Application root folder</span></span>

<span data-ttu-id="65edf-178">응용 프로그램 루트 폴더는 ASP.NET Core 템플릿으로 만든 웹 앱의 *Startup.cs* 를 포함 하는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="65edf-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="65edf-180">*/Views/_ViewImports* (MVC의 경우) 및/페이지에 대 한 */_ViewImports/* :::no-loc(Razor)::: 는 영역에서 뷰로 가져오지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for :::no-loc(Razor)::: Pages, is not imported to views in areas.</span></span> <span data-ttu-id="65edf-181">다음 방법 중 하나를 사용 하 여 모든 보기에 대 한 보기 가져오기를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="65edf-182">[응용 프로그램 루트 폴더](#arf)에 *_ViewImports* 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="65edf-183">응용 프로그램 루트 폴더의 *_ViewImports* 는 앱의 모든 보기에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="65edf-184">*_ViewImports cshtml* 파일을 영역 아래의 적절 한 보기 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="65edf-185">*_ViewImports* 파일에는 일반적으로 [태그 도우미](xref:mvc/views/tag-helpers/intro) imports, `@using` 및 문이 포함 `@inject` 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="65edf-186">자세한 내용은 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="65edf-187">보기가 저장된 기본 영역 폴더 변경</span><span class="sxs-lookup"><span data-stu-id="65edf-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="65edf-188">다음 코드는 기본 영역 폴더를 `"Areas"`에서 `"MyAreas"`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="65edf-189">페이지가 있는 영역 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="65edf-189">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="65edf-190">페이지를 포함 하는 영역 :::no-loc(Razor)::: `Areas/<area name>/Pages` 에는 앱의 루트 폴더가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-190">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="65edf-191">[예제 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)에서는 다음 폴더 구조가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="65edf-192">프로젝트 이름</span><span class="sxs-lookup"><span data-stu-id="65edf-192">Project name</span></span>
  * <span data-ttu-id="65edf-193">Areas</span><span class="sxs-lookup"><span data-stu-id="65edf-193">Areas</span></span>
    * <span data-ttu-id="65edf-194">제품</span><span class="sxs-lookup"><span data-stu-id="65edf-194">Products</span></span>
      * <span data-ttu-id="65edf-195">페이지</span><span class="sxs-lookup"><span data-stu-id="65edf-195">Pages</span></span>
        * <span data-ttu-id="65edf-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="65edf-196">_ViewImports</span></span>
        * <span data-ttu-id="65edf-197">정보</span><span class="sxs-lookup"><span data-stu-id="65edf-197">About</span></span>
        * <span data-ttu-id="65edf-198">인덱스</span><span class="sxs-lookup"><span data-stu-id="65edf-198">Index</span></span>
    * <span data-ttu-id="65edf-199">서비스</span><span class="sxs-lookup"><span data-stu-id="65edf-199">Services</span></span>
      * <span data-ttu-id="65edf-200">페이지</span><span class="sxs-lookup"><span data-stu-id="65edf-200">Pages</span></span>
        * <span data-ttu-id="65edf-201">관리</span><span class="sxs-lookup"><span data-stu-id="65edf-201">Manage</span></span>
          * <span data-ttu-id="65edf-202">정보</span><span class="sxs-lookup"><span data-stu-id="65edf-202">About</span></span>
          * <span data-ttu-id="65edf-203">인덱스</span><span class="sxs-lookup"><span data-stu-id="65edf-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="65edf-204">페이지 및 영역을 사용한 링크 생성 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="65edf-204">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="65edf-205">[예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)의 다음 코드는 영역이 지정된(예: `asp-area="Products"`) 링크 생성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="65edf-206">예제 다운로드에는 위의 링크 및 영역을 지정하지 않은 동일한 링크를 담고 있는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="65edf-207">이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="65edf-208">영역을 지정하지 않고 생성된 링크는 동일한 영역의 페이지에서 참조할 때만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="65edf-209">영역을 지정하지 않으면 *앰비언트* 값에 따라 라우팅이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="65edf-210">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="65edf-211">예제 앱에서 앰비언트 값을 사용하면 잘못된 링크가 생성되는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="65edf-212">예를 들어 다음 코드에서 생성된 링크를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="65edf-213">이전 코드에서:</span><span class="sxs-lookup"><span data-stu-id="65edf-213">For the preceding code:</span></span>

* <span data-ttu-id="65edf-214">`<a asp-page="/Manage/About">`에서 생성된 링크는 `Services` 영역의 페이지를 마지막으로 요청한 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="65edf-215">`/Services/Manage/`, `/Services/Manage/Index` 또는 `/Services/Manage/About`).</span><span class="sxs-lookup"><span data-stu-id="65edf-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="65edf-216">`<a asp-page="/About">`에서 생성된 링크는 `/Home`의 페이지를 마지막으로 요청한 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="65edf-217">이 코드는 [예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas)에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="65edf-218">_ViewImports 파일을 사용하여 네임스페이스 및 태그 도우미 가져오기</span><span class="sxs-lookup"><span data-stu-id="65edf-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="65edf-219">각 영역 *페이지* 폴더에 *_ViewImports* 파일을 추가 하 여 네임 스페이스 및 태그 도우미를 폴더의 각 페이지로 가져올 수 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="65edf-220">*_ViewImports.cshtml* 파일이 없는 샘플 코드의 *Services* 영역을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="65edf-221">다음 태그는 */Services/Manage/About* 페이지를 보여 줍니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-221">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="65edf-222">이전 태그에서:</span><span class="sxs-lookup"><span data-stu-id="65edf-222">In the preceding markup:</span></span>

* <span data-ttu-id="65edf-223">모델(`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)을 지정하려면 정규화된 도메인 이름을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="65edf-224">[태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용할 수 있습니다. `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="65edf-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="65edf-225">예제 다운로드에서 Products 영역에는 다음과 같은 *_ViewImports.cshtml* 파일이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="65edf-226">다음 태그는 */Products/About* 페이지를 보여 줍니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-226">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="65edf-227">이전 파일에서 네임 스페이스 및 지시문은 `@addTagHelper` *영역/제품/페이지/_ViewImports cshtml* 파일을 통해 파일에 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="65edf-228">자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) 및 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="65edf-229">페이지 영역에 대 한 공유 레이아웃 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="65edf-229">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="65edf-230">전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml* 을 애플리케이션 루트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="65edf-231">영역 게시</span><span class="sxs-lookup"><span data-stu-id="65edf-231">Publishing Areas</span></span>

<span data-ttu-id="65edf-232">\*.csproj 파일에 \`<Project Sdk="Microsoft.NET.Sdk.Web">\`\`이 포함되면 \*.cshtml 파일 및 *wwwroot* 디렉터리 내의 모든 파일이 출력에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="65edf-233">영역은 관련 기능을 별개의 네임스페이스(라우팅용) 및 폴더 구조(보기용)의 그룹으로 구조화하는 데 사용되는 ASP.NET 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="65edf-234">영역을 사용 하면 다른 경로 매개 변수, `area` 를 및 또는 페이지에 추가 하 여 라우팅의 용도에 대 한 계층을 만들 수 `controller` `action` :::no-loc(Razor)::: `page` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="65edf-235">영역을 사용 하 여 ASP.NET Core 웹 앱을 더 작은 기능 그룹으로 분할 하 고 각각 자체 :::no-loc(Razor)::: 페이지, 컨트롤러, 뷰 및 모델 집합을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="65edf-236">영역은 사실상 앱 내부의 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="65edf-237">ASP.NET Core 웹 프로젝트에서 페이지, 모델, 컨트롤러 및 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 보관됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="65edf-238">ASP.NET Core 런타임은 명명 규칙을 사용하여 이러한 구성 요소 간의 관계를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="65edf-239">대형 앱의 경우 앱을 별도의 고급 기능 영역으로 나누는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="65edf-240">결제, 청구 및 검색과 같은 여러 비즈니스 단위가 있는 전자상거래 앱을 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="65edf-241">이러한 각 단위에는 보기, 컨트롤러, 페이지 및 모델을 포함 하는 고유한 영역이 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-241">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="65edf-242">다음과 같은 경우 프로젝트에서 영역을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="65edf-243">앱은 논리적으로 분리할 수 있는 여러 개의 고급 기능 구성 요소로 이루어져 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="65edf-244">각 기능 영역을 독립적으로 작업할 수 있도록 앱을 나누고자 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="65edf-245">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="65edf-246">다운로드 샘플은 테스트 영역에 대한 기본 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="65edf-247">페이지를 사용 하 :::no-loc(Razor)::: 는 경우이 문서의 [ :::no-loc(Razor)::: 페이지를 포함](#areas-with-razor-pages) 하는 영역을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-247">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="65edf-248">보기가 있는 컨트롤러 영역</span><span class="sxs-lookup"><span data-stu-id="65edf-248">Areas for controllers with views</span></span>

<span data-ttu-id="65edf-249">영역, 컨트롤러 및 보기를 사용하는 일반적인 ASP.NET Core 웹앱은 다음을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="65edf-250">[영역 폴더 구조](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="65edf-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="65edf-251">[`[Area]`](#attribute)컨트롤러를 영역에 연결 하는 특성을 가진 컨트롤러:</span><span class="sxs-lookup"><span data-stu-id="65edf-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="65edf-252">[시작에 추가 된 영역 경로](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="65edf-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="65edf-253">영역 폴더 구조</span><span class="sxs-lookup"><span data-stu-id="65edf-253">Area folder structure</span></span>

<span data-ttu-id="65edf-254">*Products* 및 *Services* 의 두 논리 그룹이 존재하는 앱을 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="65edf-255">영역을 사용할 경우 폴더 구조는 다음과 유사할 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="65edf-256">프로젝트 이름</span><span class="sxs-lookup"><span data-stu-id="65edf-256">Project name</span></span>
  * <span data-ttu-id="65edf-257">Areas</span><span class="sxs-lookup"><span data-stu-id="65edf-257">Areas</span></span>
    * <span data-ttu-id="65edf-258">제품</span><span class="sxs-lookup"><span data-stu-id="65edf-258">Products</span></span>
      * <span data-ttu-id="65edf-259">Controllers</span><span class="sxs-lookup"><span data-stu-id="65edf-259">Controllers</span></span>
        * <span data-ttu-id="65edf-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="65edf-260">HomeController.cs</span></span>
        * <span data-ttu-id="65edf-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="65edf-261">ManageController.cs</span></span>
      * <span data-ttu-id="65edf-262">보기</span><span class="sxs-lookup"><span data-stu-id="65edf-262">Views</span></span>
        * <span data-ttu-id="65edf-263">홈</span><span class="sxs-lookup"><span data-stu-id="65edf-263">Home</span></span>
          * <span data-ttu-id="65edf-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-264">Index.cshtml</span></span>
        * <span data-ttu-id="65edf-265">관리</span><span class="sxs-lookup"><span data-stu-id="65edf-265">Manage</span></span>
          * <span data-ttu-id="65edf-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-266">Index.cshtml</span></span>
          * <span data-ttu-id="65edf-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-267">About.cshtml</span></span>
    * <span data-ttu-id="65edf-268">서비스</span><span class="sxs-lookup"><span data-stu-id="65edf-268">Services</span></span>
      * <span data-ttu-id="65edf-269">Controllers</span><span class="sxs-lookup"><span data-stu-id="65edf-269">Controllers</span></span>
        * <span data-ttu-id="65edf-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="65edf-270">HomeController.cs</span></span>
      * <span data-ttu-id="65edf-271">보기</span><span class="sxs-lookup"><span data-stu-id="65edf-271">Views</span></span>
        * <span data-ttu-id="65edf-272">홈</span><span class="sxs-lookup"><span data-stu-id="65edf-272">Home</span></span>
          * <span data-ttu-id="65edf-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-273">Index.cshtml</span></span>

<span data-ttu-id="65edf-274">영역을 사용할 때는 이전 레이아웃이 일반적인 반면, 이 폴더 구조를 사용하기 위해서는 보기 파일만 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="65edf-275">보기 검색은 일치하는 영역 보기 파일을 다음 순서로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="65edf-276">컨트롤러를 영역과 연결</span><span class="sxs-lookup"><span data-stu-id="65edf-276">Associate the controller with an Area</span></span>

<span data-ttu-id="65edf-277">영역 컨트롤러는 [ &lbrack; 영역 &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성으로 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="65edf-278">영역 경로 추가</span><span class="sxs-lookup"><span data-stu-id="65edf-278">Add Area route</span></span>

<span data-ttu-id="65edf-279">영역 경로는 일반적으로 특성 라우팅보다는 규칙 기반 라우팅을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="65edf-280">규칙 기반 라우팅은 순서에 영향을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="65edf-281">일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="65edf-282">모든 영역에서 url 공간이 동일한 경우 `{area:...}`를 경로 템플릿의 토큰으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="65edf-283">이전 코드에서 `exists`는 경로가 영역과 일치해야 한다는 제약 조건을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="65edf-284">`{area:...}`를 사용하는 것은 영역에 라우팅을 추가하는 가장 간단한 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="65edf-285">다음 코드는 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>를 사용하여 명명된 두 개의 영역 경로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="65edf-286">ASP.NET Core 2.2에서 `MapAreaRoute`를 사용하는 경우에는 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore/issues/7772)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="65edf-287">자세한 내용은 [영역 라우팅](xref:mvc/controllers/routing#areas)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="65edf-288">MVC 영역과 링크 생성</span><span class="sxs-lookup"><span data-stu-id="65edf-288">Link generation with MVC areas</span></span>

<span data-ttu-id="65edf-289">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)의 다음 코드는 지정된 영역과 링크 생성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="65edf-290">이전 코드로 생성된 링크는 앱의 어느 위치에서나 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="65edf-291">예제 다운로드에는 위의 링크 및 영역을 지정하지 않은 동일한 링크를 담고 있는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="65edf-292">이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="65edf-293">영역을 지정하지 않고 생성된 링크는 동일한 영역 및 컨트롤러 페이지에서 참조할 때만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="65edf-294">영역 또는 컨트롤러를 지정하지 않으면 라우팅은 *앰비언트* 값에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="65edf-295">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="65edf-296">예제 앱에서 앰비언트 값을 사용하면 잘못된 링크가 생성되는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="65edf-297">자세한 정보는 [컨트롤러 작업에 라우팅](xref:mvc/controllers/routing)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="65edf-298">_ViewStart.cshtml 파일을 사용하여 영역에 대한 레이아웃 공유</span><span class="sxs-lookup"><span data-stu-id="65edf-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="65edf-299">전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml* 을 애플리케이션 루트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="65edf-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="65edf-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="65edf-301">표준 위치의 */Views/_ViewImports.cshtml* 은 영역에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="65edf-302">사용자의 지역에서 공용 [태그 도우미](xref:mvc/views/tag-helpers/intro), 또는를 사용 하려면 `@using` 적절 한 `@inject` *_ViewImports cshtml* 파일이 [영역 보기에 적용](xref:mvc/views/layout#importing-shared-directives)되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="65edf-303">모든 보기에서 동일한 동작을 원하는 경우 */Views/_ViewImports.cshtml* 을 응용 프로그램 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="65edf-304">보기가 저장된 기본 영역 폴더 변경</span><span class="sxs-lookup"><span data-stu-id="65edf-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="65edf-305">다음 코드는 기본 영역 폴더를 `"Areas"`에서 `"MyAreas"`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="65edf-306">페이지가 있는 영역 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="65edf-306">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="65edf-307">페이지를 포함 하는 영역 :::no-loc(Razor)::: `Areas/<area name>/Pages` 에는 앱의 루트 폴더가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-307">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="65edf-308">[예제 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)에서는 다음 폴더 구조가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="65edf-309">프로젝트 이름</span><span class="sxs-lookup"><span data-stu-id="65edf-309">Project name</span></span>
  * <span data-ttu-id="65edf-310">Areas</span><span class="sxs-lookup"><span data-stu-id="65edf-310">Areas</span></span>
    * <span data-ttu-id="65edf-311">제품</span><span class="sxs-lookup"><span data-stu-id="65edf-311">Products</span></span>
      * <span data-ttu-id="65edf-312">페이지</span><span class="sxs-lookup"><span data-stu-id="65edf-312">Pages</span></span>
        * <span data-ttu-id="65edf-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="65edf-313">_ViewImports</span></span>
        * <span data-ttu-id="65edf-314">정보</span><span class="sxs-lookup"><span data-stu-id="65edf-314">About</span></span>
        * <span data-ttu-id="65edf-315">인덱스</span><span class="sxs-lookup"><span data-stu-id="65edf-315">Index</span></span>
    * <span data-ttu-id="65edf-316">서비스</span><span class="sxs-lookup"><span data-stu-id="65edf-316">Services</span></span>
      * <span data-ttu-id="65edf-317">페이지</span><span class="sxs-lookup"><span data-stu-id="65edf-317">Pages</span></span>
        * <span data-ttu-id="65edf-318">관리</span><span class="sxs-lookup"><span data-stu-id="65edf-318">Manage</span></span>
          * <span data-ttu-id="65edf-319">정보</span><span class="sxs-lookup"><span data-stu-id="65edf-319">About</span></span>
          * <span data-ttu-id="65edf-320">인덱스</span><span class="sxs-lookup"><span data-stu-id="65edf-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="65edf-321">페이지 및 영역을 사용한 링크 생성 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="65edf-321">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="65edf-322">[예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)의 다음 코드는 영역이 지정된(예: `asp-area="Products"`) 링크 생성을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="65edf-323">이전 코드로 생성된 링크는 앱의 어느 위치에서나 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="65edf-324">예제 다운로드에는 위의 링크 및 영역을 지정하지 않은 동일한 링크를 담고 있는 [부분 뷰](xref:mvc/views/partial)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="65edf-325">이 부분 뷰는 [레이아웃 파일](xref:mvc/views/layout)에서 참조되므로 앱의 모든 페이지에 생성된 링크가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="65edf-326">영역을 지정하지 않고 생성된 링크는 동일한 영역의 페이지에서 참조할 때만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="65edf-327">영역을 지정하지 않으면 *앰비언트* 값에 따라 라우팅이 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="65edf-328">현재 요청의 현재 경로 값은 링크 생성에 대한 앰비언트 값으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="65edf-329">예제 앱에서 앰비언트 값을 사용하면 잘못된 링크가 생성되는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="65edf-330">예를 들어 다음 코드에서 생성된 링크를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="65edf-331">이전 코드에서:</span><span class="sxs-lookup"><span data-stu-id="65edf-331">For the preceding code:</span></span>

* <span data-ttu-id="65edf-332">`<a asp-page="/Manage/About">`에서 생성된 링크는 `Services` 영역의 페이지를 마지막으로 요청한 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="65edf-333">`/Services/Manage/`, `/Services/Manage/Index` 또는 `/Services/Manage/About`).</span><span class="sxs-lookup"><span data-stu-id="65edf-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="65edf-334">`<a asp-page="/About">`에서 생성된 링크는 `/Home`의 페이지를 마지막으로 요청한 경우에만 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="65edf-335">이 코드는 [예제 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)에서 가져온 것입니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="65edf-336">_ViewImports 파일을 사용하여 네임스페이스 및 태그 도우미 가져오기</span><span class="sxs-lookup"><span data-stu-id="65edf-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="65edf-337">각 영역 *페이지* 폴더에 *_ViewImports* 파일을 추가 하 여 네임 스페이스 및 태그 도우미를 폴더의 각 페이지로 가져올 수 있습니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="65edf-338">*_ViewImports.cshtml* 파일이 없는 샘플 코드의 *Services* 영역을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="65edf-339">다음 태그는 */Services/Manage/About* 페이지를 보여 줍니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-339">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="65edf-340">이전 태그에서:</span><span class="sxs-lookup"><span data-stu-id="65edf-340">In the preceding markup:</span></span>

* <span data-ttu-id="65edf-341">모델(`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)을 지정하려면 정규화된 도메인 이름을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="65edf-342">[태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용할 수 있습니다. `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="65edf-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="65edf-343">예제 다운로드에서 Products 영역에는 다음과 같은 *_ViewImports.cshtml* 파일이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="65edf-344">다음 태그는 */Products/About* 페이지를 보여 줍니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="65edf-344">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="65edf-345">이전 파일에서 네임 스페이스 및 지시문은 `@addTagHelper` *영역/제품/페이지/_ViewImports cshtml* 파일을 통해 파일에 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="65edf-346">자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) 및 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="65edf-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="65edf-347">페이지 영역에 대 한 공유 레이아웃 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="65edf-347">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="65edf-348">전체 앱의 일반적인 레이아웃을 공유하려면 *_ViewStart.cshtml* 을 애플리케이션 루트 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="65edf-349">영역 게시</span><span class="sxs-lookup"><span data-stu-id="65edf-349">Publishing Areas</span></span>

<span data-ttu-id="65edf-350">\*.csproj 파일에 \`<Project Sdk="Microsoft.NET.Sdk.Web">\`\`이 포함되면 \*.cshtml 파일 및 *wwwroot* 디렉터리 내의 모든 파일이 출력에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="65edf-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
