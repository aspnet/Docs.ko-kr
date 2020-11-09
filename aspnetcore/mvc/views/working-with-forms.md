---
title: ASP.NET Core 형식의 태그 도우미
author: rick-anderson
description: 형식과 함께 사용되는 기본 제공 태그 도우미를 설명합니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: a568ad481eb09587e2ddce8e84d0ac3eff01e990
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060900"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="5d6f4-103">ASP.NET Core 형식의 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="5d6f4-104">작성자 [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) 및 [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="5d6f4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="5d6f4-105">이 문서에서는 형식 및 형식에서 일반적으로 사용되는 HTML 요소 작업을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="5d6f4-106">HTML [형식](https://www.w3.org/TR/html401/interact/forms.html) 요소는 서버에 데이터를 다시 게시하는 기본 메커니즘 웹앱을 사용하도록 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="5d6f4-107">이 문서에서는 대부분 [태그 도우미](tag-helpers/intro.md) 및 강력한 HTML 형식을 생산적으로 만들 수 있는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="5d6f4-108">이 문서를 읽기 전에 [태그 도우미 소개](tag-helpers/intro.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="5d6f4-109">많은 경우 HTML 도우미는 특정 태그 도우미에 대한 대체 방법을 제공하지만, 태그 도우미는 HTML 도우미를 대체하지 않으며 각 HTML 도우미에 대한 태그 도우미가 없다는 사실을 인지하는 것이 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="5d6f4-110">HTML 도우미 대안이 있다면 그 내용도 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="5d6f4-111">형식 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-111">The Form Tag Helper</span></span>

<span data-ttu-id="5d6f4-112">[Form](https://www.w3.org/TR/html401/interact/forms.html) 태그 도우미는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="5d6f4-113">[\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` MVC 컨트롤러 작업 또는 명명 된 경로에 대 한 HTML 특성 값을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="5d6f4-114">사이트 간 요청 위조를 방지하기 위해 숨겨진 [요청 확인 토큰](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)을 만듭니다(HTTP Post 작업 메서드에서 `[ValidateAntiForgeryToken]` 특성과 함께 사용할 경우).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="5d6f4-115">`asp-route-<Parameter Name>` 특성을 제공합니다. 여기서 `<Parameter Name>`을 경로 값에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="5d6f4-116">`Html.BeginForm` 및 `Html.BeginRouteForm`에 대한 `routeValues` 매개 변수는 유사한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="5d6f4-117">HTML 도우미 대안 `Html.BeginForm` 및 `Html.BeginRouteForm`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="5d6f4-118">샘플:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-118">Sample:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="5d6f4-119">위의 형식 태그 도우미에서는 다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-119">The Form Tag Helper above generates the following HTML:</span></span>

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="5d6f4-120">MVC 런타임은 형식 태그 도우미 특성 `asp-controller` 및 `asp-action`에서 `action` 특성 값을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="5d6f4-121">형식 태그 도우미도 사이트 간 요청 위조를 방지하기 위해 숨겨진 [요청 확인 토큰](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)을 만듭니다(HTTP Post 작업 메서드에서 `[ValidateAntiForgeryToken]` 특성과 함께 사용할 경우).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="5d6f4-122">사이트 간 요청 위조로부터 순수한 HTML 형식을 보호하기는 어렵습니다. 형식 태그 도우미는 이러한 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="5d6f4-123">명명된 경로 사용</span><span class="sxs-lookup"><span data-stu-id="5d6f4-123">Using a named route</span></span>

<span data-ttu-id="5d6f4-124">`asp-route` 태그 도우미 특성은 HTML `action` 특성에 대한 태그를 만들 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="5d6f4-125">`register`라는 [경로](../../fundamentals/routing.md)를 사용하는 앱은 등록 페이지에 다음 태그를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="5d6f4-126">*보기/계정* 폴더의 보기 중 다수( *개별 사용자 계정* 을 사용하여 새로운 웹앱을 만들 때 생성됨)에는 [asp-route-returnurl](xref:mvc/views/working-with-forms) 특성이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts* ) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="5d6f4-127">기본 제공된 템플릿을 사용하면 권한이 부여된 리소스에 액세스하려고 하지만 인증되거나 권한이 없는 경우에만 `returnUrl`이 자동으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="5d6f4-128">권한이 없는 액세스를 시도하는 경우 보안 미들웨어는 사용자를 `returnUrl` 설정이 포함된 로그인 페이지에 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="5d6f4-129">양식 작업 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="5d6f4-130">양식 작업 태그 도우미는 생성된 `<button ...>` 또는 `<input type="image" ...>` 태그의 `formaction` 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="5d6f4-131">`formaction` 특성은 양식이 해당 데이터를 제출하는 위치를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="5d6f4-132">이 클래스 [\<input>](https://www.w3.org/wiki/HTML/Elements/input) 는 형식 및 요소의 요소에 바인딩됩니다 `image` [\<button>](https://www.w3.org/wiki/HTML/Elements/button) .</span><span class="sxs-lookup"><span data-stu-id="5d6f4-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="5d6f4-133">양식 작업 태그 도우미를 통해 여러 개의 [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` 특성을 사용하여 해당 요소에 대해 생성되는 `formaction` 링크를 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="5d6f4-134">`formaction`의 값을 제어하기 위해 지원되는 [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 특성:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="5d6f4-135">attribute</span><span class="sxs-lookup"><span data-stu-id="5d6f4-135">Attribute</span></span>|<span data-ttu-id="5d6f4-136">설명</span><span class="sxs-lookup"><span data-stu-id="5d6f4-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="5d6f4-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="5d6f4-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="5d6f4-138">컨트롤러의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-138">The name of the controller.</span></span>|
|[<span data-ttu-id="5d6f4-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="5d6f4-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="5d6f4-140">작업 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-140">The name of the action method.</span></span>|
|[<span data-ttu-id="5d6f4-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="5d6f4-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="5d6f4-142">영역의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-142">The name of the area.</span></span>|
|[<span data-ttu-id="5d6f4-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="5d6f4-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="5d6f4-144">페이지의 이름 Razor 입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="5d6f4-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="5d6f4-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="5d6f4-146">페이지 처리기의 이름 Razor 입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="5d6f4-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="5d6f4-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="5d6f4-148">경로의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-148">The name of the route.</span></span>|
|[<span data-ttu-id="5d6f4-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="5d6f4-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="5d6f4-150">단일 URL 경로 값입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-150">A single URL route value.</span></span> <span data-ttu-id="5d6f4-151">예: `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="5d6f4-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="5d6f4-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="5d6f4-153">모든 경로 값입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-153">All route values.</span></span>|
|[<span data-ttu-id="5d6f4-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="5d6f4-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="5d6f4-155">URL 조각입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="5d6f4-156">컨트롤러에 제출 예제</span><span class="sxs-lookup"><span data-stu-id="5d6f4-156">Submit to controller example</span></span>

<span data-ttu-id="5d6f4-157">다음 태그는 입력 또는 단추가 선택될 때 `HomeController`의 `Index` 작업에 양식을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="5d6f4-158">위의 태그는 다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="5d6f4-159">페이지에 제출 예제</span><span class="sxs-lookup"><span data-stu-id="5d6f4-159">Submit to page example</span></span>

<span data-ttu-id="5d6f4-160">다음 태그는 폼을 페이지에 제출 합니다 `About` Razor .</span><span class="sxs-lookup"><span data-stu-id="5d6f4-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="5d6f4-161">위의 태그는 다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="5d6f4-162">경로에 제출 예제</span><span class="sxs-lookup"><span data-stu-id="5d6f4-162">Submit to route example</span></span>

<span data-ttu-id="5d6f4-163">`/Home/Test` 엔드포인트를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="5d6f4-164">다음 태그는 `/Home/Test` 엔드포인트에 양식을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="5d6f4-165">위의 태그는 다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="5d6f4-166">입력 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-166">The Input Tag Helper</span></span>

<span data-ttu-id="5d6f4-167">입력 태그 도우미는 [\<input>](https://www.w3.org/wiki/HTML/Elements/input) razor 뷰의 모델 식에 HTML 요소를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="5d6f4-168">구문</span><span class="sxs-lookup"><span data-stu-id="5d6f4-168">Syntax:</span></span>

```cshtml
<input asp-for="<Expression Name>">
```

<span data-ttu-id="5d6f4-169">입력 태그 도우미:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="5d6f4-170">`asp-for` 특성에 지정된 식 이름에 대해 `id` 및 `name` HTML 특성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="5d6f4-171">`asp-for="Property1.Property2"`은 `m => m.Property1.Property2`와 동등합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="5d6f4-172">식의 이름은 `asp-for` 특성 값에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="5d6f4-173">추가 정보는 [식 이름](#expression-names) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="5d6f4-174">모델 속성에 적용된 모델 형식 및 [데이터 주석](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) 특성에 따라 HTML `type` 특성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="5d6f4-175">HTML `type` 특성 값이 지정 된 경우 덮어쓰지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="5d6f4-176">모델 속성에 적용되는 [데이터 주석](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) 특성에서 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) 유효성 검사 특성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="5d6f4-177">`Html.TextBoxFor` 및 `Html.EditorFor`와 HTML 도우미 기능이 겹칩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="5d6f4-178">자세한 내용은 **입력 태그 도우미에 대한 HTML 도우미 대안** 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="5d6f4-179">강력한 형식 지정을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-179">Provides strong typing.</span></span> <span data-ttu-id="5d6f4-180">속성의 이름이 변경되고 태그 도우미를 업데이트하지 않은 경우 다음과 비슷한 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

<span data-ttu-id="5d6f4-181">`Input` 태그 도우미는 .NET 형식을 기반으로 HTML `type` 특성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="5d6f4-182">다음 표에서는 몇 가지 일반적인 .NET 형식 및 생성된 HTML 형식을 나열합니다(.NET 형식의 일부만 나열됨).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="5d6f4-183">.NET 형식</span><span class="sxs-lookup"><span data-stu-id="5d6f4-183">.NET type</span></span>|<span data-ttu-id="5d6f4-184">입력 형식</span><span class="sxs-lookup"><span data-stu-id="5d6f4-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="5d6f4-185">Bool</span><span class="sxs-lookup"><span data-stu-id="5d6f4-185">Bool</span></span>|<span data-ttu-id="5d6f4-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-186">type="checkbox"</span></span>|
|<span data-ttu-id="5d6f4-187">String</span><span class="sxs-lookup"><span data-stu-id="5d6f4-187">String</span></span>|<span data-ttu-id="5d6f4-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-188">type="text"</span></span>|
|<span data-ttu-id="5d6f4-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="5d6f4-189">DateTime</span></span>|<span data-ttu-id="5d6f4-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="5d6f4-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="5d6f4-191">Byte</span><span class="sxs-lookup"><span data-stu-id="5d6f4-191">Byte</span></span>|<span data-ttu-id="5d6f4-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-192">type="number"</span></span>|
|<span data-ttu-id="5d6f4-193">Int</span><span class="sxs-lookup"><span data-stu-id="5d6f4-193">Int</span></span>|<span data-ttu-id="5d6f4-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-194">type="number"</span></span>|
|<span data-ttu-id="5d6f4-195">Single, Double</span><span class="sxs-lookup"><span data-stu-id="5d6f4-195">Single, Double</span></span>|<span data-ttu-id="5d6f4-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-196">type="number"</span></span>|

<span data-ttu-id="5d6f4-197">다음 표에서는 입력 태그 도우미가 특정 입력 형식에 매핑되는 몇 가지 일반적인 [데이터 주석](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) 특성을 보여줍니다(유효성 검사 특성의 일부만 나열됨).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="5d6f4-198">attribute</span><span class="sxs-lookup"><span data-stu-id="5d6f4-198">Attribute</span></span>|<span data-ttu-id="5d6f4-199">입력 형식</span><span class="sxs-lookup"><span data-stu-id="5d6f4-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="5d6f4-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="5d6f4-200">[EmailAddress]</span></span>|<span data-ttu-id="5d6f4-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-201">type="email"</span></span>|
|<span data-ttu-id="5d6f4-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="5d6f4-202">[Url]</span></span>|<span data-ttu-id="5d6f4-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-203">type="url"</span></span>|
|<span data-ttu-id="5d6f4-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="5d6f4-204">[HiddenInput]</span></span>|<span data-ttu-id="5d6f4-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-205">type="hidden"</span></span>|
|<span data-ttu-id="5d6f4-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="5d6f4-206">[Phone]</span></span>|<span data-ttu-id="5d6f4-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-207">type="tel"</span></span>|
|<span data-ttu-id="5d6f4-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="5d6f4-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="5d6f4-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-209">type="password"</span></span>|
|<span data-ttu-id="5d6f4-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="5d6f4-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="5d6f4-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-211">type="date"</span></span>|
|<span data-ttu-id="5d6f4-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="5d6f4-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="5d6f4-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="5d6f4-213">type="time"</span></span>|

<span data-ttu-id="5d6f4-214">샘플:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="5d6f4-215">위의 코드는 다음과 같은 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-215">The code above generates the following HTML:</span></span>

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="5d6f4-216">`Email` 및 `Password` 속성에 적용할 데이터 주석은 모델에서 메타데이터를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="5d6f4-217">입력 태그 도우미는 모델 메타데이터를 사용하고 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` 특성을 생성합니다([모델 유효성 검사](../models/validation.md) 참조).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="5d6f4-218">이러한 특성에서는 입력 필드에 연결할 유효성 검사기를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="5d6f4-219">이 기능은 비간섭 HTML5 및 [jQuery](https://jquery.com/) 유효성 검사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="5d6f4-220">가 중이 아닌 특성에는 형식이 있습니다 `data-val-rule="Error Message"` . 여기서 rule은 유효성 검사 규칙의 이름 (예:,, 등 `data-val-required` `data-val-email` )입니다 `data-val-maxlength` . 특성에 오류 메시지가 제공 되 면 특성에 대 한 값으로 표시 됩니다 `data-val-rule` .</span><span class="sxs-lookup"><span data-stu-id="5d6f4-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="5d6f4-221">또한 규칙에 대한 추가 세부 정보를 제공하는 `data-val-ruleName-argumentName="argumentValue"` 형식의 특성이 있습니다(예: `data-val-maxlength-max="1024"`).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="5d6f4-222">입력 태그 도우미에 대한 HTML 도우미 대안</span><span class="sxs-lookup"><span data-stu-id="5d6f4-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="5d6f4-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` 및 `Html.EditorFor`에는 입력 태그 도우미와 겹치는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="5d6f4-224">입력 태그 도우미는 `type` 특성을 자동으로 설정하는 반면 `Html.TextBox` 및 `Html.TextBoxFor`는 설정하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="5d6f4-225">`Html.Editor` 및 `Html.EditorFor`는 컬렉션, 복잡한 개체 및 템플릿을 처리하는 반면 입력 태그 도우미는 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="5d6f4-226">입력 태그 도우미인 `Html.EditorFor` 및 `Html.TextBoxFor`은 강력한 형식이 지정(람다 식 사용)되는 반면 `Html.TextBox` 및 `Html.Editor`는 지정되지 않습니다(식 이름 사용).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="5d6f4-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="5d6f4-227">HtmlAttributes</span></span>

<span data-ttu-id="5d6f4-228">`@Html.Editor()` 및 `@Html.EditorFor()`는 해당 기본 템플릿을 실행할 때 `htmlAttributes`라는 특수한 `ViewDataDictionary` 항목을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="5d6f4-229">이 동작은 필요에 따라 `additionalViewData` 매개 변수를 사용하여 확대됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="5d6f4-230">"htmlAttributes" 키는 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="5d6f4-231">"htmlAttributes" 키는 `@Html.TextBox()`와 같은 입력 도우미에 전달된 `htmlAttributes` 개체와 유사하게 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="5d6f4-232">식 이름</span><span class="sxs-lookup"><span data-stu-id="5d6f4-232">Expression names</span></span>

<span data-ttu-id="5d6f4-233">`asp-for` 특성 값은 `ModelExpression`이며 람다 식의 오른쪽입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="5d6f4-234">따라서 `asp-for="Property1"`은 생성된 코드에서 `m => m.Property1`이 됩니다. 따라서 `Model`과 함께 접두사로 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="5d6f4-235">“\@” 문자를 사용하여 인라인 식을 시작하고 `m.` 앞으로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

<span data-ttu-id="5d6f4-236">다음 항목을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-236">Generates the following:</span></span>

```html
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="5d6f4-237">컬렉션 속성을 가진 `asp-for="CollectionProperty[23].Member"`은 `i`에 `23` 값이 포함될 경우 `asp-for="CollectionProperty[i].Member"`와 동일한 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="5d6f4-238">ASP.NET Core MVC가 `ModelExpression`의 값을 계산하는 경우 `ModelState`를 비롯한 여러 원본을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="5d6f4-239">`<input type="text" asp-for="@Name">`을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="5d6f4-240">계산된 `value` 특성은 첫 번째 null이 아닌 값입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="5d6f4-241">"Name" 키를 가진 `ModelState` 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="5d6f4-242">식 `Model.Name`의 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="5d6f4-243">자식 속성 탐색</span><span class="sxs-lookup"><span data-stu-id="5d6f4-243">Navigating child properties</span></span>

<span data-ttu-id="5d6f4-244">보기 모델의 속성 경로를 사용하여 자식 속성을 탐색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="5d6f4-245">자식 `Address` 속성을 포함하는 보다 복잡한 모델 클래스를 사용해보세요.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="5d6f4-246">보기에서 `Address.AddressLine1`에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="5d6f4-247">다음 HTML이 `Address.AddressLine1`에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="5d6f4-248">식 이름 및 컬렉션</span><span class="sxs-lookup"><span data-stu-id="5d6f4-248">Expression names and Collections</span></span>

<span data-ttu-id="5d6f4-249">샘플, `Colors`의 배열을 포함하는 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="5d6f4-250">작업 방법:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

<span data-ttu-id="5d6f4-251">다음은 Razor 특정 요소에 액세스 하는 방법을 보여 줍니다 `Color` .</span><span class="sxs-lookup"><span data-stu-id="5d6f4-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="5d6f4-252">*Views/Shared/EditorTemplates/String.cshtml* 템플릿:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="5d6f4-253">`List<T>`를 사용하는 샘플:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="5d6f4-254">다음은 Razor 컬렉션을 반복 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="5d6f4-255">*Views/Shared/EditorTemplates/ToDoItem.cshtml* 템플릿:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="5d6f4-256">값이 `asp-for` 또는 `Html.DisplayFor` 해당 컨텍스트에서 사용될 때 가능한 경우 `foreach`를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="5d6f4-257">일반적으로, `for`는 열거자를 할당할 필요가 없으므로 `foreach`보다 좋습니다(시나리오에서 허용하는 경우). 그러나 LINQ 식에서 인덱서를 평가하는 작업은 비용이 많이 들기 때문에 최소화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="5d6f4-258">위의 주석 처리된 코드 샘플은 람다 식을 `@` 연산자와 바꿔서 목록에 있는 각 `ToDoItem`에 액세스하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="5d6f4-259">텍스트 영역 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="5d6f4-260">`Textarea Tag Helper` 태그 도우미는 입력 태그 도우미와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="5d6f4-261">`id` `name` 요소에 대 한 모델에서 및 특성과 데이터 유효성 검사 특성을 생성 합니다 [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) .</span><span class="sxs-lookup"><span data-stu-id="5d6f4-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="5d6f4-262">강력한 형식 지정을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-262">Provides strong typing.</span></span>

* <span data-ttu-id="5d6f4-263">HTML 도우미 대안: `Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="5d6f4-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="5d6f4-264">샘플:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="5d6f4-265">다음 HTML이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-265">The following HTML is generated:</span></span>

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="5d6f4-266">레이블 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-266">The Label Tag Helper</span></span>

* <span data-ttu-id="5d6f4-267">`for` [\<label>](https://www.w3.org/wiki/HTML/Elements/label) 식 이름에 대해 요소에서 레이블 캡션과 특성을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="5d6f4-268">HTML 도우미 대안: `Html.LabelFor`</span><span class="sxs-lookup"><span data-stu-id="5d6f4-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="5d6f4-269">`Label Tag Helper`는 HTML 레이블 요소를 통해 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="5d6f4-270">`Display` 특성에서 설명 레이블을 값을 자동으로 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="5d6f4-271">의도한 표시 이름은 시간에 따라 변경될 수 있고, `Display` 특성 및 레이블 태그 도우미의 조합은 사용되는 모든 곳에서 `Display`을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="5d6f4-272">소스 코드의 간단한 태그</span><span class="sxs-lookup"><span data-stu-id="5d6f4-272">Less markup in source code</span></span>

* <span data-ttu-id="5d6f4-273">모델 속성을 사용하는 강력한 형식화입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-273">Strong typing with the model property.</span></span>

<span data-ttu-id="5d6f4-274">샘플:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="5d6f4-275">다음 HTML이 `<label>` 요소에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-275">The following HTML is generated for the `<label>` element:</span></span>

```html
<label for="Email">Email Address</label>
```

<span data-ttu-id="5d6f4-276">레이블 태그 도우미는 "이메일"의 `for` 특성 값을 생성했습니다. 이 값은 `<input>` 요소와 연결된 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="5d6f4-277">태그 도우미는 일관된 `id` 및 `for` 요소를 제대로 연결할 수 있도록 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="5d6f4-278">이 샘플의 캡션은 `Display` 특성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="5d6f4-279">모델에 `Display` 특성이 포함되지 않는 경우 캡션은 식의 속성 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="5d6f4-280">유효성 검사 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="5d6f4-281">두 개의 유효성 검사 태그 도우미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="5d6f4-282">`Validation Message Tag Helper`(모델의 단일 속성에 대한 유효성 검사 메시지 표시) 및 `Validation Summary Tag Helper`(유효성 검사 오류의 요약 표시)입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="5d6f4-283">`Input Tag Helper`는 모델 클래스의 데이터 주석 특성에 따라 입력 요소에 HTML5 클라이언트 쪽 유효성 검사 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="5d6f4-284">유효성 검사도 서버에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-284">Validation is also performed on the server.</span></span> <span data-ttu-id="5d6f4-285">유효성 검사 태그 도우미는 유효성 검사 오류가 발생하는 경우 이러한 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="5d6f4-286">유효성 검사 메시지 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="5d6f4-287">[HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` 특성을 [범위](https://developer.mozilla.org/docs/Web/HTML/Element/span) 요소에 추가합니다. 그러면 지정된 모델 속성의 입력 필드에서 유효성 검사 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="5d6f4-288">클라이언트 쪽 유효성 검사 오류가 발생할 때 [jQuery](https://jquery.com/)는 `<span>` 요소에서 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="5d6f4-289">유효성 검사도 서버에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-289">Validation also takes place on the server.</span></span> <span data-ttu-id="5d6f4-290">클라이언트는 JavaScript를 사용하지 않도록 설정할 수 있고 일부 유효성 검사는 서버 쪽에서만 수행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="5d6f4-291">HTML 도우미 대안: `Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="5d6f4-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="5d6f4-292">`Validation Message Tag Helper`는 HTML [범위](https://developer.mozilla.org/docs/Web/HTML/Element/span) 요소에서 `asp-validation-for` 특성과 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```cshtml
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="5d6f4-293">유효성 검사 메시지 태그 도우미는 다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="5d6f4-294">일반적으로 동일한 속성에서 `Input` 태그 도우미 이후에 `Validation Message Tag Helper`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="5d6f4-295">이렇게 하면 오류가 발생하는 입력 주변에서 유효성 검사 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="5d6f4-296">클라이언트 쪽 유효성 검사 대신 올바른 JavaScript 및 [jQuery](https://jquery.com/) 스크립트 참조를 사용하는 보기가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="5d6f4-297">자세한 내용은 [모델 유효성 검사](../models/validation.md) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="5d6f4-298">서버 쪽 유효성 검사 오류가 발생하는 경우(예: 사용자 지정 서버 쪽 유효성 검사 또는 클라이언트 쪽 유효성 검사를 사용하지 않는 경우) MVC는 해당 오류 메시지를 `<span>` 요소의 본문으로 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="5d6f4-299">유효성 검사 요약 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="5d6f4-300">`asp-validation-summary` 특성이 있는 `<div>` 요소를 대상으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="5d6f4-301">HTML 도우미 대안: `@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="5d6f4-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="5d6f4-302">`Validation Summary Tag Helper`는 유효성 검사 메시지의 요약 정보를 표시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="5d6f4-303">`asp-validation-summary` 특성 값은 다음 중 하나일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="5d6f4-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="5d6f4-304">asp-validation-summary</span></span>|<span data-ttu-id="5d6f4-305">표시되는 유효성 검사 메시지</span><span class="sxs-lookup"><span data-stu-id="5d6f4-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="5d6f4-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="5d6f4-306">ValidationSummary.All</span></span>|<span data-ttu-id="5d6f4-307">속성 및 모델 수준</span><span class="sxs-lookup"><span data-stu-id="5d6f4-307">Property and model level</span></span>|
|<span data-ttu-id="5d6f4-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="5d6f4-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="5d6f4-309">모델</span><span class="sxs-lookup"><span data-stu-id="5d6f4-309">Model</span></span>|
|<span data-ttu-id="5d6f4-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="5d6f4-310">ValidationSummary.None</span></span>|<span data-ttu-id="5d6f4-311">없음</span><span class="sxs-lookup"><span data-stu-id="5d6f4-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="5d6f4-312">예제</span><span class="sxs-lookup"><span data-stu-id="5d6f4-312">Sample</span></span>

<span data-ttu-id="5d6f4-313">다음 예제에서 데이터 모델에는 `DataAnnotation` 특성이 있습니다. 이 특성은 `<input>` 요소에 대한 유효성 검사 오류 메시지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="5d6f4-314">유효성 검사 오류가 발생하는 경우 유효성 검사 태그 도우미는 다음 오류 메시지를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="5d6f4-315">생성된 HTML(모델은 유효한 경우):</span><span class="sxs-lookup"><span data-stu-id="5d6f4-315">The generated HTML (when the model is valid):</span></span>

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="5d6f4-316">선택 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="5d6f4-316">The Select Tag Helper</span></span>

* <span data-ttu-id="5d6f4-317">모델의 속성에 대한 [선택](https://www.w3.org/wiki/HTML/Elements/select) 및 관련된 [옵션](https://www.w3.org/wiki/HTML/Elements/option) 요소를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="5d6f4-318">HTML 도우미 대안 `Html.DropDownListFor` 및 `Html.ListBoxFor`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="5d6f4-319">`Select Tag Helper` `asp-for`는 [선택](https://www.w3.org/wiki/HTML/Elements/select) 요소에 대한 모델 속성 이름을 지정하고 `asp-items`는 [옵션](https://www.w3.org/wiki/HTML/Elements/option) 요소를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="5d6f4-320">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-320">For example:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="5d6f4-321">샘플:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="5d6f4-322">`Index` 메서드는 `CountryViewModel`를 초기화하고, 선택한 국가를 설정하고, `Index` 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="5d6f4-323">HTTP POST `Index` 메서드는 선택 항목을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="5d6f4-324">`Index` 보기:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="5d6f4-325">다음 HTML을 생성합니다("CA"를 선택함).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="5d6f4-326">선택 태그 도우미와 함께 `ViewBag` 또는 `ViewData`를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="5d6f4-327">보기 모델은 일반적으로 더 강력하고 문제가 적은 방식으로 MVC 메타데이터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="5d6f4-328">`asp-for` 특성 값은 특별한 경우이며 다른 태그 도우미 특성(예: `asp-items`)과 달리 `Model` 접두사를 필요로 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="5d6f4-329">열거형 바인딩</span><span class="sxs-lookup"><span data-stu-id="5d6f4-329">Enum binding</span></span>

<span data-ttu-id="5d6f4-330">`enum` 속성과 함께 `<select>`를 사용하고 `enum` 값에서 `SelectListItem` 요소를 생성하는 것이 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="5d6f4-331">샘플:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="5d6f4-332">`GetEnumSelectList` 메서드는 열거형에 대해 `SelectList` 개체를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="5d6f4-333">`Display` 특성으로 열거자 목록을 표시하여 보다 풍부한 UI를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="5d6f4-334">다음 HTML이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-334">The following HTML is generated:</span></span>

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="5d6f4-335">옵션 그룹</span><span class="sxs-lookup"><span data-stu-id="5d6f4-335">Option Group</span></span>

<span data-ttu-id="5d6f4-336">HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) 요소는 뷰 모델에 하나 이상의 개체가 포함 된 경우에 생성 됩니다 `SelectListGroup` .</span><span class="sxs-lookup"><span data-stu-id="5d6f4-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="5d6f4-337">`CountryViewModelGroup`은 `SelectListItem` 요소를 "북아메리카" 및 "유럽" 그룹으로 그룹화합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="5d6f4-338">두 개의 그룹은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-338">The two groups are shown below:</span></span>

![옵션 그룹 예제](working-with-forms/_static/grp.png)

<span data-ttu-id="5d6f4-340">생성되는 HTML은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-340">The generated HTML:</span></span>

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="5d6f4-341">다중 선택</span><span class="sxs-lookup"><span data-stu-id="5d6f4-341">Multiple select</span></span>

<span data-ttu-id="5d6f4-342">`asp-for` 특성에 지정된 속성이 `IEnumerable`인 경우 태그 선택 도우미는 [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) 특성을 자동으로 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="5d6f4-343">예를 들어, 다음과 같은 모델을 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="5d6f4-344">다음 보기에서:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-344">With the following view:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="5d6f4-345">다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-345">Generates the following HTML:</span></span>

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="5d6f4-346">선택 영역 없음</span><span class="sxs-lookup"><span data-stu-id="5d6f4-346">No selection</span></span>

<span data-ttu-id="5d6f4-347">여러 페이지에서 "지정 안 됨" 옵션을 사용하는 경우 HTML의 반복을 제거하는 템플릿을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="5d6f4-348">*Views/Shared/EditorTemplates/CountryViewModel.cshtml* 템플릿:</span><span class="sxs-lookup"><span data-stu-id="5d6f4-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="5d6f4-349">HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) 요소를 추가 하는 경우는 *선택 하지 않는* 것으로 제한 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="5d6f4-350">예를 들어 다음과 같은 보기 및 작업 메서드는 위의 코드와 유사한 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5d6f4-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="5d6f4-351">현재 `Country` 값에 따라 올바른 `<option>` 요소가 선택됩니다(`selected="selected"` 특성 포함).</span><span class="sxs-lookup"><span data-stu-id="5d6f4-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="5d6f4-352">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="5d6f4-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="5d6f4-353">HTML 양식 요소</span><span class="sxs-lookup"><span data-stu-id="5d6f4-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="5d6f4-354">요청 확인 토큰</span><span class="sxs-lookup"><span data-stu-id="5d6f4-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="5d6f4-355">IAttributeAdapter 인터페이스</span><span class="sxs-lookup"><span data-stu-id="5d6f4-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="5d6f4-356">이 문서의 코드 조각</span><span class="sxs-lookup"><span data-stu-id="5d6f4-356">Code snippets for this document</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
