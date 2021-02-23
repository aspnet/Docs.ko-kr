---
title: 3부. ASP.NET Core MVC 앱에 뷰 추가
author: rick-anderson
description: ASP.NET Core MVC에 대한 자습서 시리즈의 3부입니다.
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804612"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="94b86-103">3부. ASP.NET Core MVC 앱에 뷰 추가</span><span class="sxs-lookup"><span data-stu-id="94b86-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="94b86-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94b86-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94b86-105">이 섹션에서는 [Razor](xref:mvc/views/razor) 뷰 파일을 사용하도록 `HelloWorldController` 클래스를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="94b86-106">그러면 클라이언트에 대한 HTML 응답을 생성하는 프로세스가 깔끔하게 캡슐화됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="94b86-107">Razor를 사용하여 뷰 템플릿을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-107">View templates are created using Razor.</span></span> <span data-ttu-id="94b86-108">Razor 기반 뷰 템플릿은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-108">Razor-based view templates:</span></span>

* <span data-ttu-id="94b86-109">*.cshtml* 파일 확장명을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="94b86-110">C#으로 HTML 출력을 만드는 세련된 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="94b86-111">현재 `Index` 메서드는 컨트롤러 클래스에서 메시지와 함께 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="94b86-112">`HelloWorldController` 클래스에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="94b86-113">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="94b86-113">The preceding code:</span></span>

* <span data-ttu-id="94b86-114">컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="94b86-115">뷰 템플릿을 사용하여 HTML 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="94b86-116">컨트롤러 메서드:</span><span class="sxs-lookup"><span data-stu-id="94b86-116">Controller methods:</span></span>

* <span data-ttu-id="94b86-117">‘작업 메서드’라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="94b86-118">예를 들어 위의 코드에는 `Index` 작업 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="94b86-119">일반적으로 `string` 같은 형식이 아니라 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 클래스 또는 <xref:Microsoft.AspNetCore.Mvc.IActionResult>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="94b86-120">보기 추가</span><span class="sxs-lookup"><span data-stu-id="94b86-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94b86-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94b86-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94b86-122">*Views* 폴더를 마우스 오른쪽 단추로 클릭한 다음, **추가 > 새 폴더** 를 클릭하고 폴더의 이름을 *HelloWorld* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="94b86-123">*Views/HelloWorld* 폴더를 마우스 오른쪽 단추로 클릭한 다음, **추가 > 새 항목** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="94b86-124">**새 항목 추가 - MvcMovie** 대화 상자에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="94b86-125">오른쪽 위의 검색 상자에 *뷰* 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="94b86-126">**Razor 뷰** 선택</span><span class="sxs-lookup"><span data-stu-id="94b86-126">Select **Razor View**</span></span>
* <span data-ttu-id="94b86-127">*이름* 상자 값은 **Index.cshtml** 을 유지하세요.</span><span class="sxs-lookup"><span data-stu-id="94b86-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="94b86-128">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-128">Select **Add**</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="94b86-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94b86-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94b86-131">`HelloWorldController`에 대해 `Index` 뷰를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="94b86-132">*Views/HelloWorld* 라는 새 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="94b86-133">*Views/HelloWorld* 폴더에 *Index.cshtml* 이라는 이름으로 새 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94b86-134">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94b86-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94b86-135">Control 키를 누른 상태에서 *Views* 폴더를 클릭한 다음, **추가 > 새 폴더** 를 클릭하고 폴더의 이름을 *HelloWorld* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="94b86-136">Control 키를 누른 상태에서 *Views/HelloWorld* 폴더를 클릭한 다음, **추가 > 새 파일** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="94b86-137">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="94b86-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="94b86-138">왼쪽 창에서 **ASP.NET Core** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="94b86-139">가운데 창에서 **Razor 뷰 - 비어 있음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="94b86-140">**이름** 상자에 *Index* 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="94b86-141">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-141">Select **New**.</span></span>

  ![새 항목 추가 대화 상자](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="94b86-143">*Views/HelloWorld/Index.cshtml* Razor 뷰 파일의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="94b86-144">`https://localhost:{PORT}/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="94b86-145">`HelloWorldController`의 `Index` 메서드는 `return View();` 문을 실행했습니다. 이 문은 메서드가 뷰 템플릿 파일을 사용하여 브라우저에 대한 응답을 렌더링해야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="94b86-146">뷰 템플릿 파일 이름을 지정하지 않았으므로 MVC는 기본 뷰 파일을 사용하도록 기본 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="94b86-147">뷰 파일 이름을 지정하지 않으면 기본 뷰가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="94b86-148">이 예제에서 기본 뷰는 작업 메서드 `Index`와 동일한 이름을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="94b86-149">뷰 템플릿 */Views/HelloWorld/Index.cshtml* 이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="94b86-150">다음 이미지는 뷰에 하드 코딩된 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="94b86-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="94b86-151">문자열을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-151">hard-coded in the view:</span></span>

  ![브라우저 창](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="94b86-153">보기 및 레이아웃 페이지 변경</span><span class="sxs-lookup"><span data-stu-id="94b86-153">Change views and layout pages</span></span>

<span data-ttu-id="94b86-154">메뉴 링크 **MvcMovie**, **Home** 및 **Privacy** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="94b86-155">각 페이지는 동일한 메뉴 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="94b86-156">메뉴 레이아웃은 *Views/Shared/_Layout.cshtml* 파일에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="94b86-157">*Views/Shared/_Layout.cshtml* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="94b86-158">[레이아웃](xref:mvc/views/layout) 템플릿은 다음을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="94b86-159">사이트의 HTML 컨테이너 레이아웃을 한 곳에 지정</span><span class="sxs-lookup"><span data-stu-id="94b86-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="94b86-160">사이트의 여러 페이지에 HTML 컨테이너 레이아웃 적용</span><span class="sxs-lookup"><span data-stu-id="94b86-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="94b86-161">`@RenderBody()` 줄을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="94b86-162">`RenderBody`는 개발자가 만든 모든 보기 전용 페이지가 표시되는 자리 표시자이며 레이아웃 페이지으로 *래핑됩니다*.</span><span class="sxs-lookup"><span data-stu-id="94b86-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="94b86-163">예를 들어 **Privacy** 링크를 선택하는 경우 **Views/Home/Privacy.cshtml** 보기가 `RenderBody` 메서드 내부에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="94b86-164">레이아웃 파일의 제목, 바닥글 및 메뉴 링크 변경</span><span class="sxs-lookup"><span data-stu-id="94b86-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="94b86-165">*Views/Shared/_Layout.cshtml* 파일의 내용을 다음 태그로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="94b86-166">변경 내용이 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94b86-167">위에 태그에서는 다음과 같은 변경을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="94b86-168">세 번 나오는 `MvcMovie`를 `Movie App`으로 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="94b86-169">앵커 요소 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>`를 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`로 변경했습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="94b86-170">이 앱은 [영역](xref:mvc/controllers/areas)을 사용하지 않기 때문에 위의 태그에서 `asp-area=""` [앵커 태그 도우미 특성](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 및 특성 값이 생략되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="94b86-171">**참고**: `Movies` 컨트롤러는 구현되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="94b86-172">지금은 `Movie App` 링크가 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="94b86-173">변경 내용을 저장하고 **Privacy** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="94b86-174">브라우저 탭의 제목으로 **Privacy Policy - Mvc Movie** 대신 **Privacy Policy - Movie App** 이 표시됨을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![개인 정보 탭](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="94b86-176">**홈** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-176">Select the **Home** link.</span></span>

<span data-ttu-id="94b86-177">제목 및 앵커 텍스트에 **Movie App** 이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="94b86-178">레이아웃 템플릿은 한 번 변경되었으며 사이트의 모든 페이지에 새 링크 텍스트 및 새 제목이 반영됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="94b86-179">*Views/_ViewStart.cshtml* 파일을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="94b86-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="94b86-180">*Views/_ViewStart.cshtml* 파일은 각 보기로 *Views/Shared/_Layout.cshtml* 파일을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="94b86-181">`Layout` 속성을 사용하여 다른 레이아웃 보기를 설정하거나 레이아웃 파일을 사용하지 않도록 `null`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="94b86-182">*Views/HelloWorld/Index.cshtml* 뷰 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="94b86-183">다음에 강조 표시된 대로 제목과 `<h2>` 요소를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="94b86-184">제목과 `<h2>` 요소가 약간 다르기 때문에 코드의 어떤 부분에서 표시가 변경되는지가 명확합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="94b86-185">위의 코드에서 `ViewData["Title"] = "Movie List";`는 `ViewData` 사전의 `Title` 속성을 "Movie List"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="94b86-186">`Title` 속성은 레이아웃 페이지의 `<title>` HTML 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="94b86-187">변경 내용을 저장하고 `https://localhost:{PORT}/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="94b86-188">다음 사항이 변경되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="94b86-189">브라우저 제목</span><span class="sxs-lookup"><span data-stu-id="94b86-189">Browser title.</span></span>
* <span data-ttu-id="94b86-190">기본 제목</span><span class="sxs-lookup"><span data-stu-id="94b86-190">Primary heading.</span></span>
* <span data-ttu-id="94b86-191">보조 제목</span><span class="sxs-lookup"><span data-stu-id="94b86-191">Secondary headings.</span></span>

<span data-ttu-id="94b86-192">브라우저에 변경 내용이 없으면 보고 있는 캐시된 콘텐츠일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="94b86-193">브라우저에서 Ctrl+F5를 눌러 서버의 응답이 강제로 로드되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="94b86-194">브라우저 제목은 *Index.cshtml* 보기 템플릿에서 설정한 `ViewData["Title"]` 및 레이아웃 파일에 추가된 추가적인 "- Movie App"으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="94b86-195">*Index.cshtml* 보기 템플릿의 콘텐츠가 *Views/Shared/_Layout.cshtml* 보기 템플릿과 병합되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="94b86-196">단일 HTML 응답이 브라우저로 전송되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="94b86-197">레이아웃 템플릿을 사용하면 응용 프로그램의 모든 페이지에 걸쳐 적용되는 변경 내용을 쉽게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="94b86-198">자세한 내용은 [레이아웃](xref:mvc/views/layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94b86-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![영화 목록 보기](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="94b86-200">작은 양의 “데이터”인 “Hello in the View Template!”</span><span class="sxs-lookup"><span data-stu-id="94b86-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="94b86-201">메시지가 하드 코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-201">message, is hard-coded however.</span></span> <span data-ttu-id="94b86-202">MVC 애플리케이션에는 “V”(뷰)와 “C”(컨트롤러)가 있지만 “M”(모델)은 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="94b86-203">컨트롤러에서 보기로 데이터 전달</span><span class="sxs-lookup"><span data-stu-id="94b86-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="94b86-204">컨트롤러 작업은 들어오는 URL 요청에 대한 응답으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="94b86-205">컨트롤러 클래스는 들어오는 브라우저 요청을 처리하는 코드가 작성된 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="94b86-206">컨트롤러는 데이터 원본에서 데이터를 검색하고 브라우저에 다시 전송할 응답의 유형을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="94b86-207">보기 템플릿은 브라우저에 대한 HTML 응답을 만들고 서식을 지정하기 위해서 컨트롤러에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="94b86-208">컨트롤러는 보기 템플릿에서 응답을 렌더링하기 위해 필요한 데이터를 제공할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="94b86-209">뷰 템플릿은 다음을 수행하면 **안 됩니다**.</span><span class="sxs-lookup"><span data-stu-id="94b86-209">View templates should **not**:</span></span>

* <span data-ttu-id="94b86-210">비즈니스 논리 수행</span><span class="sxs-lookup"><span data-stu-id="94b86-210">Do business logic</span></span>
* <span data-ttu-id="94b86-211">데이터베이스와 직접 상호 작용</span><span class="sxs-lookup"><span data-stu-id="94b86-211">Interact with a database directly.</span></span>

<span data-ttu-id="94b86-212">뷰 템플릿은 컨트롤러에서 제공하는 데이터만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="94b86-213">이 “문제의 분리”를 유지 관리하면 코드를 다음과 같이 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="94b86-214">정리</span><span class="sxs-lookup"><span data-stu-id="94b86-214">Clean.</span></span>
* <span data-ttu-id="94b86-215">테스트 가능</span><span class="sxs-lookup"><span data-stu-id="94b86-215">Testable.</span></span>
* <span data-ttu-id="94b86-216">유지 관리 가능</span><span class="sxs-lookup"><span data-stu-id="94b86-216">Maintainable.</span></span>

<span data-ttu-id="94b86-217">현재 `HelloWorldController` 클래스에서 `Welcome` 메서드는 `name` 및 `ID` 매개 변수를 가져온 다음 값을 브라우저로 직접 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="94b86-218">컨트롤러가 이 응답을 문자열로 렌더링하게 하는 대신, 보기 템플릿을 사용하도록 컨트롤러를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="94b86-219">보기 템플릿은 동적 응답을 생성합니다. 즉, 응답을 생성하려면 컨트롤러에서 뷰로 적절한 데이터를 전달해야 한다는 의미입니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="94b86-220">컨트롤러가 뷰 템플릿에 필요한 동적 데이터(매개 변수)를 `ViewData` 사전에 넣도록 하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="94b86-221">그러면 뷰 템플릿이 동적 데이터에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="94b86-222">*HelloWorldController.cs* 에서 `Message` 및 `NumTimes` 값을 `ViewData` 사전에 추가하도록 `Welcome` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="94b86-223">`ViewData` 사전은 동적 개체로, 모든 형식을 사용할 수 있다는 의미입니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="94b86-224">`ViewData` 개체는 항목이 추가될 때까지 정의된 속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="94b86-225">[MVC 모델 바인딩 시스템](xref:mvc/models/model-binding)은 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수(`name` 및 `numTimes`)를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="94b86-226">완료된 `HelloWorldController`는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="94b86-227">`ViewData` 사전 개체는 보기에 전달될 데이터를 담고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="94b86-228">*Views/HelloWorld/Welcome.cshtml* 이라는 환영 보기 템플릿을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="94b86-229">*Welcome.cshtml* 보기 템플릿에 "Hello" `NumTimes`를 표시하는 반복을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="94b86-230">*Views/HelloWorld/Welcome.cshtml* 의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="94b86-231">변경 내용을 저장하고 다음 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="94b86-232">데이터는 URL에서 가져와서 [MVC 모델 바인더](xref:mvc/models/model-binding)를 사용하여 컨트롤러에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="94b86-233">컨트롤러는 데이터를 `ViewData` 사전으로 패키징하고 해당 개체를 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="94b86-234">그런 다음 보기는 데이터를 HTML로 브라우저에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-234">The view then renders the data as HTML to the browser.</span></span>

![Welcome 레이블과 네 번 표시되는 Hello Rick 문장을 보여주는 Welcome 보기](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="94b86-236">위 샘플에서는 컨트롤러에서 뷰로 데이터를 전달하는 데 `ViewData` 사전이 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="94b86-237">자습서의 뒷부분에서는 컨트롤러에서 보기로 데이터를 전달하기 위해 보기 모델이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="94b86-238">데이터를 전달하는 뷰 모델 접근 방식이 `ViewData` 사전 접근 방식보다 선호됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="94b86-239">다음 자습서에서는 영화 데이터베이스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="94b86-240">[이전: 컨트롤러 추가](adding-controller.md)
> [다음: 모델 추가](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="94b86-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94b86-241">이 섹션에서는 [Razor](xref:mvc/views/razor) 뷰 파일을 사용하도록 `HelloWorldController` 클래스를 수정하여 클라이언트에 대한 HTML 응답을 생성하는 프로세스를 깔끔하게 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="94b86-242">Razor를 사용하여 뷰 템플릿 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-242">You create a view template file using Razor.</span></span> <span data-ttu-id="94b86-243">Razor 기반 뷰 템플릿의 파일 확장명은 *.cshtml* 입니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="94b86-244">C#으로 HTML 출력을 만드는 세련된 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="94b86-245">현재 `Index` 메서드는 컨트롤러 클래스에서 하드 코딩된 메시지 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="94b86-246">`HelloWorldController` 클래스에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="94b86-247">앞의 코드는 컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="94b86-248">HTML 응답을 생성하기 위해 뷰 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="94b86-249">위의 `Index` 메서드와 같은 컨트롤러 메서드(‘동작 메서드’라고도 함)는 일반적으로 `string`과 같은 형식이 아닌 <xref:Microsoft.AspNetCore.Mvc.IActionResult>(또는 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 클래스)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="94b86-250">보기 추가</span><span class="sxs-lookup"><span data-stu-id="94b86-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94b86-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94b86-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94b86-252">*Views* 폴더를 마우스 오른쪽 단추로 클릭한 다음, **추가 > 새 폴더** 를 클릭하고 폴더의 이름을 *HelloWorld* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="94b86-253">*Views/HelloWorld* 폴더를 마우스 오른쪽 단추로 클릭한 다음, **추가 > 새 항목** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="94b86-254">**새 항목 추가 - MvcMovie** 대화 상자에서</span><span class="sxs-lookup"><span data-stu-id="94b86-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="94b86-255">오른쪽 위의 검색 상자에 *뷰* 를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="94b86-256">**Razor 뷰** 선택</span><span class="sxs-lookup"><span data-stu-id="94b86-256">Select **Razor View**</span></span>

  * <span data-ttu-id="94b86-257">*이름* 상자 값은 **Index.cshtml** 을 유지하세요.</span><span class="sxs-lookup"><span data-stu-id="94b86-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="94b86-258">**추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-258">Select **Add**</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="94b86-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94b86-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94b86-261">`HelloWorldController`에 `Index` 보기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="94b86-262">*Views/HelloWorld* 라는 새 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="94b86-263">*Views/HelloWorld* 폴더에 *Index.cshtml* 이라는 이름으로 새 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94b86-264">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94b86-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94b86-265">마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더** 를 차례로 클릭하고 폴더의 이름을 *HelloWorld* 로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="94b86-266">마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 파일** 을 차례로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="94b86-267">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="94b86-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="94b86-268">왼쪽 창에서 **웹** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="94b86-269">가운데 창에서 **빈 HTML 파일** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="94b86-270">**이름** 상자에 *Index.cshtml* 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="94b86-271">**새로 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-271">Select **New**.</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="94b86-273">*Views/HelloWorld/Index.cshtml* Razor 뷰 파일의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="94b86-274">`https://localhost:{PORT}/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="94b86-275">`HelloWorldController`의 `Index` 메서드는 많은 작업을 수행하지 않았습니다. `return View();` 문만 실행합니다. 해당 문은 메서드가 브라우저에 대한 응답을 렌더링하기 위해 보기 템플릿 파일을 사용해야 함을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="94b86-276">보기 템플릿 파일 이름을 지정하지 않았기 때문에 MVC는 기본적으로 기본 보기 파일을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="94b86-277">기본 보기 파일은 메서드(`Index`)와 이름이 같으므로 */Views/HelloWorld/Index.cshtml* 이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="94b86-278">아래 이미지는 보기에 하드 코딩된 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="94b86-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="94b86-279">문자열을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-279">hard-coded in the view.</span></span>

![브라우저 창](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="94b86-281">보기 및 레이아웃 페이지 변경</span><span class="sxs-lookup"><span data-stu-id="94b86-281">Change views and layout pages</span></span>

<span data-ttu-id="94b86-282">메뉴 링크를 선택합니다(**MvcMovie**, **Home** 및 **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="94b86-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="94b86-283">각 페이지는 동일한 메뉴 레이아웃을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="94b86-284">메뉴 레이아웃은 *Views/Shared/_Layout.cshtml* 파일에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="94b86-285">*Views/Shared/_Layout.cshtml* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="94b86-286">[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한 곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트의 여러 페이지에 걸쳐 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="94b86-287">`@RenderBody()` 줄을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="94b86-288">`RenderBody`는 개발자가 만든 모든 보기 전용 페이지가 표시되는 자리 표시자이며 레이아웃 페이지으로 *래핑됩니다*.</span><span class="sxs-lookup"><span data-stu-id="94b86-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="94b86-289">예를 들어 **Privacy** 링크를 선택하는 경우 **Views/Home/Privacy.cshtml** 보기가 `RenderBody` 메서드 내부에 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="94b86-290">레이아웃 파일의 제목, 바닥글 및 메뉴 링크 변경</span><span class="sxs-lookup"><span data-stu-id="94b86-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="94b86-291">제목 및 바닥글 요소에서 `MvcMovie`를 `Movie App`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="94b86-292">앵커 요소 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>`를 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="94b86-293">다음 태그는 강조 표시된 변경 내용을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="94b86-294">이 앱은 [영역](xref:mvc/controllers/areas)을 사용하지 않기 때문에 위의 태그에서 `asp-area` [앵커 태그 도우미 특성](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)이 생략되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="94b86-295">**참고**: `Movies` 컨트롤러는 아직 구현되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="94b86-296">이 시점에서 `Movie App` 링크는 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="94b86-297">변경 내용을 저장하고 **Privacy** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="94b86-298">브라우저 탭의 제목으로 **Privacy Policy - Mvc Movie** 대신 **Privacy Policy - Movie App** 이 표시됨을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![개인 정보 탭](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="94b86-300">**Home** 링크를 선택하고 제목 및 앵커 텍스트가 **Movie App** 을 표시하는지도 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="94b86-301">레이아웃 템플릿에서 변경 사항을 한 번만 적용하여 사이트의 모든 페이지에 새 링크 텍스트 및 새 제목을 반영할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="94b86-302">*Views/_ViewStart.cshtml* 파일을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="94b86-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="94b86-303">*Views/_ViewStart.cshtml* 파일은 각 보기로 *Views/Shared/_Layout.cshtml* 파일을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="94b86-304">`Layout` 속성을 사용하여 다른 레이아웃 보기를 설정하거나 레이아웃 파일을 사용하지 않도록 `null`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="94b86-305">*Views/HelloWorld/Index.cshtml* 보기 파일의 제목과 `<h2>` 요소를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="94b86-306">제목과 `<h2>` 요소가 약간 다르기 때문에 어떤 코드가 표시를 변경하는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="94b86-307">위의 코드에서 `ViewData["Title"] = "Movie List";`는 `ViewData` 사전의 `Title` 속성을 "Movie List"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="94b86-308">`Title` 속성은 레이아웃 페이지의 `<title>` HTML 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="94b86-309">변경 내용을 저장하고 `https://localhost:{PORT}/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="94b86-310">브라우저 제목, 기본 제목 및 작은 제목이 변경된 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="94b86-311">(브라우저에서 변경 내용을 확인할 수 없는 경우 캐시된 콘텐츠를 보고 있을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="94b86-312">브라우저에서 Ctrl+F5 키를 눌러서 로드될 응답을 강제로 서버에서 가져옵니다.) 브라우저 제목은 *Index.cshtml* 보기 템플릿에서 설정한 `ViewData["Title"]` 및 레이아웃 파일에 추가된 추가적인 "- Movie App"으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="94b86-313">또한 *Index.cshtml* 보기 템플릿에서 콘텐츠가 *Views/Shared/_Layout.cshtml* 보기 템플릿으로 병합되고 단일 HTML 응답이 브라우저로 전송된 방식을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="94b86-314">레이아웃 템플릿을 사용하면 애플리케이션의 모든 페이지에 걸쳐 적용되는 변경 내용을 쉽게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="94b86-315">자세한 내용은 [레이아웃](xref:mvc/views/layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94b86-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![영화 목록 보기](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="94b86-317">그렇지만 일부 “데이터”(이 경우 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="94b86-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="94b86-318">메시지)는 하드 코딩되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-318">message) is hard-coded, though.</span></span> <span data-ttu-id="94b86-319">이 MVC 애플리케이션에는 "V"(보기)가 있고 "C"(컨트롤러)가 있지만 아직 "M"(모델)은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="94b86-320">컨트롤러에서 보기로 데이터 전달</span><span class="sxs-lookup"><span data-stu-id="94b86-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="94b86-321">컨트롤러 작업은 들어오는 URL 요청에 대한 응답으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="94b86-322">컨트롤러 클래스는 들어오는 브라우저 요청을 처리하는 코드가 작성된 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="94b86-323">컨트롤러는 데이터 원본에서 데이터를 검색하고 브라우저에 다시 전송할 응답의 유형을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="94b86-324">보기 템플릿은 브라우저에 대한 HTML 응답을 만들고 서식을 지정하기 위해서 컨트롤러에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="94b86-325">컨트롤러는 보기 템플릿에서 응답을 렌더링하기 위해 필요한 데이터를 제공할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="94b86-326">모범 사례 에 따라 보기 템플릿은 비즈니스 논리를 수행하거나 데이터베이스와 직접 상호 작용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="94b86-327">대신 보기 템플릿은 컨트롤러가 제공하는 데이터만 이용해서 작업해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="94b86-328">이 "문제의 분리"를 유지 관리하면 코드를 깨끗하고, 테스트 가능하고 유지 관리 가능하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="94b86-329">현재 `HelloWorldController` 클래스에서 `Welcome` 메서드는 `name` 및 `ID` 매개 변수를 가져온 다음 값을 브라우저로 직접 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="94b86-330">컨트롤러가 이 응답을 문자열로 렌더링하게 하는 대신, 보기 템플릿을 사용하도록 컨트롤러를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="94b86-331">보기 템플릿은 동적으로 응답을 생성하는데, 이는 다시 말해서 응답을 생성하기 위해 컨트롤러에서 보기로 일부 적절한 데이터를 전달해야 한다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="94b86-332">컨트롤러가 보기 템플릿에 필요한 동적 데이터(매개 변수)를 보기 템플릿이 액세스 할 수 있는 `ViewData` 사전에 넣음으로써 이를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="94b86-333">*HelloWorldController.cs* 에서 `Message` 및 `NumTimes` 값을 `ViewData` 사전에 추가하도록 `Welcome` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="94b86-334">`ViewData`사전은 동적 개체로, 이는 어떤 형식이든지 사용할 수 있음을 의미합니다. `ViewData` 개체에 무언가 넣을 때까지 이 개체에는 정의된 속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="94b86-335">[MVC 모델 바인딩 시스템](xref:mvc/models/model-binding)은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수(`name` 및 `numTimes`)를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="94b86-336">전체 *HelloWorldController.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="94b86-337">`ViewData` 사전 개체는 보기에 전달될 데이터를 담고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="94b86-338">*Views/HelloWorld/Welcome.cshtml* 이라는 환영 보기 템플릿을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="94b86-339">*Welcome.cshtml* 보기 템플릿에 "Hello" `NumTimes`를 표시하는 반복을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="94b86-340">*Views/HelloWorld/Welcome.cshtml* 의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="94b86-341">변경 내용을 저장하고 다음 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="94b86-342">데이터는 URL에서 가져와서 [MVC 모델 바인더](xref:mvc/models/model-binding)를 사용하여 컨트롤러에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="94b86-343">컨트롤러는 데이터를 `ViewData` 사전으로 패키징하고 해당 개체를 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="94b86-344">그런 다음 보기는 데이터를 HTML로 브라우저에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-344">The view then renders the data as HTML to the browser.</span></span>

![Welcome 레이블과 네 번 표시되는 Hello Rick 문장을 보여주는 Welcome 보기](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="94b86-346">위의 예제에서는 컨트롤러에서 보기로 데이터를 전달하는 데 `ViewData` 사전이 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="94b86-347">자습서의 뒷부분에서는 컨트롤러에서 보기로 데이터를 전달하기 위해 보기 모델이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="94b86-348">데이터를 전달하기 위해 보기 모델을 사용하는 방식이 일반적으로 `ViewData` 사전 방식보다 훨씬 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="94b86-349">자세한 내용은 [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)(ViewBag, ViewData 또는 TempData를 사용하는 경우)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94b86-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="94b86-350">다음 자습서에서는 영화 데이터베이스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="94b86-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="94b86-351">[이전](adding-controller.md)
> [다음](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="94b86-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
