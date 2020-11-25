---
title: ASP.NET Core의 Razor Pages 소개
author: Rick-Anderson
description: 페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor Pages를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 설명합니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: bc5f3c85d7ace120dec8369f4550a097b4f258ce
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417645"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="5e12d-103">ASP.NET Core의 Razor Pages 소개</span><span class="sxs-lookup"><span data-stu-id="5e12d-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="5e12d-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="5e12d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="5e12d-105">Razor Pages를 사용하면 컨트롤러 및 뷰를 사용하는 것보다 더 쉽고 생산적으로 코딩 페이지에 초점을 맞춘 시나리오를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="5e12d-106">모델-뷰-컨트롤러 방식을 사용하는 자습서를 찾고 있다면 [ASP.NET Core MVC 시작하기](xref:tutorials/first-mvc-app/start-mvc)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="5e12d-107">이 문서에서는 Razor Pages에 대해 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="5e12d-108">단계별 자습서가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="5e12d-109">섹션 일부 내용이 너무 고급이라면 [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="5e12d-110">ASP.NET Core에 대한 개요는 [ASP.NET Core 소개](xref:index)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e12d-111">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5e12d-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="5e12d-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e12d-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e12d-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e12d-114">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="5e12d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e12d-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e12d-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e12d-117">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="5e12d-118">Razor Pages 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="5e12d-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e12d-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e12d-120">Razor Pages 프로젝트를 만드는 방법에 대한 자세한 내용은 [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e12d-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e12d-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5e12d-122">명령줄에서 `dotnet new webapp`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e12d-123">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5e12d-124">Razor Pages 프로젝트를 만드는 방법에 대한 자세한 내용은 [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="5e12d-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5e12d-125">Razor Pages</span></span>

<span data-ttu-id="5e12d-126">Razor Pages는 *Startup.cs* 에서 사용할 수 있게 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="5e12d-127">기본적인 페이지를 살펴보겠습니다. <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="5e12d-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="5e12d-128">앞의 코드는 컨트롤러 및 뷰가 포함된 ASP.NET Core 앱에서 사용되는 [Razor 뷰 파일](xref:tutorials/first-mvc-app/adding-view)과 매우 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="5e12d-129">차이점은 [`@page`](xref:mvc/views/razor#page) 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="5e12d-130">`@page`는 파일을 MVC 액션으로 만드는데, 이 말은 컨트롤러를 거치지 않고 이 파일이 요청을 직접 처리한다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="5e12d-131">`@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="5e12d-132">`@page`는 다른 [Razor](xref:mvc/views/razor) 구문의 동작에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="5e12d-133">Razor Pages 파일 이름에는 *.cshtml* 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="5e12d-134">다음 두 파일은 `PageModel` 클래스를 사용하는 비슷한 페이지를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="5e12d-135">*Pages/Index2.cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="5e12d-136">*Pages/Index2.cshtml.cs* 페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="5e12d-137">규칙에 따라 `PageModel` 클래스 파일의 이름은 *.cs* 가 추가된 Razor Page 파일의 이름과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="5e12d-138">예를 들어 위의 Razor Page는 *Pages/Index2.cshtml* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="5e12d-139">그리고 `PageModel` 클래스가 포함된 파일의 이름은 *Pages/Index2.cshtml.cs* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="5e12d-140">페이지에 대한 URL 경로 연결은 파일 시스템 상의 페이지 위치에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="5e12d-141">다음 표는 Razor Page 경로 및 그와 일치하는 URL을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="5e12d-142">파일 이름 및 경로</span><span class="sxs-lookup"><span data-stu-id="5e12d-142">File name and path</span></span>               | <span data-ttu-id="5e12d-143">일치하는 URL</span><span class="sxs-lookup"><span data-stu-id="5e12d-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5e12d-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="5e12d-145">`/` 또는 `/Index`</span><span class="sxs-lookup"><span data-stu-id="5e12d-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="5e12d-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="5e12d-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="5e12d-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="5e12d-149">`/Store` 또는 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="5e12d-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="5e12d-150">메모:</span><span class="sxs-lookup"><span data-stu-id="5e12d-150">Notes:</span></span>

* <span data-ttu-id="5e12d-151">런타임은 기본적으로 *Pages* 폴더에서 Razor Pages 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="5e12d-152">URL에 페이지가 지정되어 있지 않을 경우 `Index`가 기본 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="5e12d-153">기본적인 양식 작성하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-153">Write a basic form</span></span>

<span data-ttu-id="5e12d-154">Razor Pages는 앱을 만들 때 웹 브라우저에서 사용되는 일반적인 패턴을 손쉽게 구현할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="5e12d-155">[모델 바인딩](xref:mvc/models/model-binding), [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 HTML 도우미는 모두 Razor Page 클래스에 정의된 속성을 통해서 ‘정확하게 작동’합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="5e12d-156">`Contact` 모델에 대한 기본적인 "연락처" 양식을 구현하는 페이지를 생각해보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="5e12d-157">이 문서의 예제에서 `DbContext`는 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) 파일에서 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="5e12d-158">메모리 내 데이터베이스에는 `Microsoft.EntityFrameworkCore.InMemory` NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="5e12d-159">데이터 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="5e12d-160">db 컨텍스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="5e12d-161">*Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="5e12d-162">*Pages/Create.cshtml.cs* 페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="5e12d-163">규약에 따라 `PageModel` 클래스의 이름은 `<PageName>Model`로 지정하며 페이지와 동일한 네임스페이스에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="5e12d-164">`PageModel` 클래스를 사용하면 페이지의 논리를 페이지의 표현으로부터 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="5e12d-165">이 클래스는 페이지로 전송된 요청에 대한 페이지 처리기와 페이지를 렌더링하기 위해 사용되는 데이터를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="5e12d-166">이러한 분리를 통해 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-166">This separation allows:</span></span>

* <span data-ttu-id="5e12d-167">[종속성 주입](xref:fundamentals/dependency-injection)을 통해 페이지 종속성 관리</span><span class="sxs-lookup"><span data-stu-id="5e12d-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="5e12d-168">유닛 테스트</span><span class="sxs-lookup"><span data-stu-id="5e12d-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="5e12d-169">이 페이지에는 `POST` 요청 시(사용자가 양식을 게시할 때) 실행되는 `OnPostAsync` *처리기 메서드* 가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="5e12d-170">모든 HTTP 동사에 대한 처리기 메서드를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="5e12d-171">가장 일반적인 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-171">The most common handlers are:</span></span>

* <span data-ttu-id="5e12d-172">`OnGet`: 페이지에 필요한 상태를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="5e12d-173">위의 코드에서 `OnGet` 메서드는 *CreateModel.cshtml* Razor Page를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="5e12d-174">`OnPost`: 양식 제출을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="5e12d-175">`Async` 명명 접미사는 선택 사항이지만 비동기 함수에 대한 규약으로 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="5e12d-176">위의 코드는 Razor Pages의 일반적인 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="5e12d-177">컨트롤러 및 뷰를 사용하는 ASP.NET 앱에 대해 잘 알고 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="5e12d-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="5e12d-178">앞 예제의 `OnPostAsync` 코드는 일반적인 컨트롤러 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="5e12d-179">[모델 바인딩](xref:mvc/models/model-binding), [유효성 검사](xref:mvc/models/validation) 및 작업 결과와 같은 MVC의 기본적인 기능들이 대부분 컨트롤러 및 Razor Pages와 동일하게 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="5e12d-180">기존 `OnPostAsync` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="5e12d-181">그리고 `OnPostAsync`의 기본적인 흐름은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="5e12d-182">유효성 검사 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-182">Check for validation errors.</span></span>

* <span data-ttu-id="5e12d-183">오류가 없는 경우 데이터를 저장하고 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="5e12d-184">오류가 있을 경우 유효성 검사 메시지가 포함된 페이지를 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="5e12d-185">대부분의 경우 유효성 검사 오류는 클라이언트에서 감지되어 서버에는 제출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="5e12d-186">*Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="5e12d-187">*Pages/Create.cshtml* 에서 렌더링된 HTML:</span><span class="sxs-lookup"><span data-stu-id="5e12d-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="5e12d-188">이전 코드에서 양식 게시:</span><span class="sxs-lookup"><span data-stu-id="5e12d-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="5e12d-189">유효한 데이터 사용:</span><span class="sxs-lookup"><span data-stu-id="5e12d-189">With valid data:</span></span>

  * <span data-ttu-id="5e12d-190">`OnPostAsync` 처리기 메서드는 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> 도우미 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="5e12d-191">`RedirectToPage`는 <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>의 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="5e12d-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="5e12d-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="5e12d-193">작업 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-193">Is an action result.</span></span>
    * <span data-ttu-id="5e12d-194">`RedirectToAction` 또는 `RedirectToRoute`(컨트롤러 및 뷰에서 사용됨)와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="5e12d-195">페이지에 맞게 사용자 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-195">Is customized for pages.</span></span> <span data-ttu-id="5e12d-196">위의 예제에서 이 메서드는 루트 인덱스 페이지(`/Index`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="5e12d-197">`RedirectToPage`는 [페이지에 대한 URL 생성](#url_gen) 섹션에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="5e12d-198">서버에 전달되는 유효성 검사 오류 포함:</span><span class="sxs-lookup"><span data-stu-id="5e12d-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="5e12d-199">`OnPostAsync` 처리기 메서드는 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> 도우미 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="5e12d-200">`Page`는 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>의 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="5e12d-201">`Page`를 반환하는 것은 컨트롤의 액션에서 `View`를 반환하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="5e12d-202">`PageResult`는 처리기 메서드의 기본 반환 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="5e12d-203">`void`를 반환하는 처리기 메서드는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="5e12d-204">앞의 예제에서 값이 없는 양식을 게시하면 false를 반환하는 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="5e12d-205">이 샘플에서는 클라이언트에 유효성 검사 오류가 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="5e12d-206">유효성 검사 오류 처리는 이 문서의 뒷부분에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="5e12d-207">클라이언트 쪽 유효성 검사에서 검색된 유효성 검사 오류 포함:</span><span class="sxs-lookup"><span data-stu-id="5e12d-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="5e12d-208">데이터가 서버에 게시되지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="5e12d-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="5e12d-209">클라이언트 쪽 유효성 검사는 이 문서의 뒷부분에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="5e12d-210">`Customer` 속성은 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 특성을 이용하여 모델 바인딩에 옵트인(opt in)합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="5e12d-211">클라이언트에서 변경하면 안 되는 속성을 포함하는 모델에서는 `[BindProperty]`를 사용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="5e12d-212">자세한 내용은 [초과 게시](xref:data/ef-rp/crud#overposting)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="5e12d-213">Razor Pages는 기본적으로 비 `GET` 동사에 대해서만 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="5e12d-214">속성에 바인딩하면 HTTP 데이터를 모델 형식으로 변환하는 코드를 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="5e12d-215">바인딩은 양식 필드 렌더링할 때와 (`<input asp-for="Customer.Name">`) 입력을 받아들일 때 동일한 속성을 사용하여 코드를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="5e12d-216">*Pages/Create.cshtml* 뷰 파일 검토:</span><span class="sxs-lookup"><span data-stu-id="5e12d-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="5e12d-217">앞의 코드에서 [입력 태그 도우미](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />`은 HTML `<input>` 요소를 `Customer.Name` 모델 식에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="5e12d-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)는 태그 도우미를 사용할 수 있도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="5e12d-219">홈페이지</span><span class="sxs-lookup"><span data-stu-id="5e12d-219">The home page</span></span>

<span data-ttu-id="5e12d-220">*Index.cshtml* 은 홈페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="5e12d-221">연결된 `PageModel` 클래스(*Index.cshtml.cs*)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5e12d-222">*Index.cshtml* 파일은 다음 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="5e12d-223">이 `<a /a>` [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)는 편집 페이지에 대한 링크를 생성하기 위해 `asp-route-{value}` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="5e12d-224">링크에는 연락처 ID와 함께 경로 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="5e12d-225">예: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="5e12d-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="5e12d-226">[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="5e12d-227">*Index.cshtml* 파일에는 각 고객 연락처에 대한 삭제 단추를 만들기 위한 태그가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="5e12d-228">렌더링된 HTML:</span><span class="sxs-lookup"><span data-stu-id="5e12d-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="5e12d-229">삭제 단추가 HTML로 렌더링되는 경우 해당 [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction)에는 다음에 대한 매개 변수가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="5e12d-230">`asp-route-id` 특성으로 지정된 고객 연락처 ID.</span><span class="sxs-lookup"><span data-stu-id="5e12d-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="5e12d-231">`asp-page-handler` 특성으로 지정된 `handler`.</span><span class="sxs-lookup"><span data-stu-id="5e12d-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="5e12d-232">단추를 선택하면 양식의 `POST` 요청이 서버로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="5e12d-233">규약에 따라 처리기 메서드의 이름은 `handler` 매개 변수의 값을 기반으로 `OnPost[handler]Async` 체계에 의해 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="5e12d-234">이번 예제에서는 `handler`가 `delete`이므로 `POST` 요청을 처리하기 위해 `OnPostDeleteAsync` 처리기 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="5e12d-235">`asp-page-handler`가 `remove` 같은 다른 값으로 설정되면 `OnPostRemoveAsync`라는 이름의 처리기 메서드가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="5e12d-236">`OnPostDeleteAsync` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="5e12d-237">쿼리 문자열에서 `id`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="5e12d-238">`FindAsync`를 사용하여 데이터베이스에서 고객 연락처를 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="5e12d-239">고객 연락처가 발견되면 제거하고 데이터베이스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="5e12d-240"><xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>를 호출하여 루트 인덱스 페이지(`/Index`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="5e12d-241">Edit.cshtml 파일</span><span class="sxs-lookup"><span data-stu-id="5e12d-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="5e12d-242">첫 번째 줄에는 `@page "{id:int}"` 지시문이 작성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="5e12d-243">라우팅 제약 조건인 `"{id:int}"`는 `int` 경로 데이터가 포함된 이 페이지에 대한 요청만 허용하도록 페이지에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="5e12d-244">페이지에 대한 요청에 `int`로 변환할 수 있는 경로 데이터가 없으면 런타임이 HTTP 404 (찾을 수 없음) 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="5e12d-245">ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="5e12d-246">*Edit.cshtml.cs* 파일:</span><span class="sxs-lookup"><span data-stu-id="5e12d-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="5e12d-247">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="5e12d-247">Validation</span></span>

<span data-ttu-id="5e12d-248">유효성 검사 규칙:</span><span class="sxs-lookup"><span data-stu-id="5e12d-248">Validation rules:</span></span>

* <span data-ttu-id="5e12d-249">모델 클래스에서 선언적으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="5e12d-250">앱의 모든 위치에서 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="5e12d-251"><xref:System.ComponentModel.DataAnnotations> 네임스페이스는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="5e12d-252">DataAnnotations는 또한 서식 지정을 돕는 [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)과 같은 서식 지정 특성을 포함하며 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="5e12d-253">`Customer` 모델 고려:</span><span class="sxs-lookup"><span data-stu-id="5e12d-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="5e12d-254">다음 *Create.cshtml* 뷰파일 사용:</span><span class="sxs-lookup"><span data-stu-id="5e12d-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="5e12d-255">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="5e12d-255">The preceding code:</span></span>

* <span data-ttu-id="5e12d-256">jQuery 및 jQuery 유효성 검사 스크립트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="5e12d-257">`<div />` 및 `<span />` [태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하여 다음을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="5e12d-258">클라이언트 쪽 유효성 검사.</span><span class="sxs-lookup"><span data-stu-id="5e12d-258">Client-side validation.</span></span>
  * <span data-ttu-id="5e12d-259">유효성 검사 오류 렌더링.</span><span class="sxs-lookup"><span data-stu-id="5e12d-259">Validation error rendering.</span></span>

* <span data-ttu-id="5e12d-260">다음 HTML을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="5e12d-261">이름 값 없이 만들기 양식을 게시하면 “이름 필드는 필수입니다.”라는 오류 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="5e12d-262">양식에서.</span><span class="sxs-lookup"><span data-stu-id="5e12d-262">on the form.</span></span> <span data-ttu-id="5e12d-263">클라이언트에서 JavaScript를 사용하는 경우 서버에 게시하지 않고 브라우저에 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="5e12d-264">`[StringLength(10)]` 특성은 렌더링된 HTML에서 `data-val-length-max="10"`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="5e12d-265">`data-val-length-max`는 브라우저에서 지정된 최대 길이를 초과하여 입력하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="5e12d-266">[Fiddler](https://www.telerik.com/fiddler)와 같은 도구를 사용하여 게시물을 편집하고 재생하는 경우:</span><span class="sxs-lookup"><span data-stu-id="5e12d-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="5e12d-267">이름이 10글자보다 긴 경우.</span><span class="sxs-lookup"><span data-stu-id="5e12d-267">With the name longer than 10.</span></span>
* <span data-ttu-id="5e12d-268">“필드 이름은 최대 길이가 10글자인 문자열이어야 합니다.”라는 오류 메시지</span><span class="sxs-lookup"><span data-stu-id="5e12d-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="5e12d-269">가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-269">is returned.</span></span>

<span data-ttu-id="5e12d-270">다음 `Movie` 모델을 보세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="5e12d-271">이 유효성 검사 특성은 적용되는 모델 속성에 시행하려는 동작을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="5e12d-272">`Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 하지만 사용자가 이 유효성 검사를 만족하기 위해 공백을 입력하는 것을 막을 수 없다는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="5e12d-273">`RegularExpression` 특성은 입력할 수 있는 문자를 제한하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="5e12d-274">이전 코드에서 “Genre”는 다음 조건을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="5e12d-275">문자만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-275">Must only use letters.</span></span>
  * <span data-ttu-id="5e12d-276">첫 번째 문자는 대문자여야 합니다</span><span class="sxs-lookup"><span data-stu-id="5e12d-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="5e12d-277">공백, 숫자 및 특수 문자는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="5e12d-278">`RegularExpression` “Rating”은 다음 조건을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="5e12d-279">첫 번째 문자는 대문자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="5e12d-280">이어진 공백에서는 특수 문자와 숫자가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="5e12d-281">“PG-13”은 등급에서는 유효하지만 “Genre”에서는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="5e12d-282">`Range` 특성은 지정한 범위 내로 값을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="5e12d-283">`StringLength` 특성은 문자열 속성의 최대 길이와, 필요에 따라 최소 길이를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="5e12d-284">값 형식(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필수적이며 `[Required]` 특성이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="5e12d-285">`Movie` 모델에 대한 만들기 페이지에 잘못된 값이 포함된 오류가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![여러 jQuery 클라이언트 쪽 유효성 검사 오류가 있는 동영상 보기 양식](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="5e12d-287">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-287">For more information, see:</span></span>

* [<span data-ttu-id="5e12d-288">영화 앱에 유효성 검사 추가</span><span class="sxs-lookup"><span data-stu-id="5e12d-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="5e12d-289">[ASP.NET Core의 모델 유효성 검사](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5e12d-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="5e12d-290">OnGet 처리기 대체를 사용하여 HEAD 요청 처리</span><span class="sxs-lookup"><span data-stu-id="5e12d-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="5e12d-291">`HEAD` 요청을 사용하면 특정 리소스의 헤더를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="5e12d-292">`GET` 요청과는 달리 `HEAD` 요청은 응답 본문을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="5e12d-293">일반적으로 `HEAD` 요청에 대한 `OnHead` 처리기를 만들고 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="5e12d-294">정의된 `OnHead` 처리기가 없다면 Razor Pages가 `OnGet` 처리기 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="5e12d-295">XSRF/CSRF 및 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5e12d-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="5e12d-296">[위조 방지 유효성 검사](xref:security/anti-request-forgery)를 통해 Razor Pages를 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="5e12d-297">[FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)는 위조 방지 토큰을 HTML 양식 요소에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="5e12d-298">Razor Pages에서 레이아웃, 부분 뷰, 템플릿 및 태그 도우미 사용하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="5e12d-299">Pages는 Razor 뷰 엔진의 모든 기능과 함께 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="5e12d-300">레이아웃, 부분 뷰, 템플릿, 태그 도우미, *_ViewStart.cshtml* 및 *_ViewImports.cshtml* 은 기존의 Razor 뷰와 동일한 방식으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="5e12d-301">이 기능들 중 일부를 활용하여 페이지를 개선해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="5e12d-302">[레이아웃 페이지](xref:mvc/views/layout)를 *Pages/Shared/_Layout.cshtml* 에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="5e12d-303">[레이아웃](xref:mvc/views/layout)은 다음과 같은 역할을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="5e12d-304">각 페이지의 레이아웃을 제어합니다(페이지가 명시적으로 레이아웃을 사용하지 않는 경우 제외).</span><span class="sxs-lookup"><span data-stu-id="5e12d-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="5e12d-305">JavaScript 및 스타일시트 같은 HTML 구조를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="5e12d-306">Razor 페이지의 내용은 `@RenderBody()`가 호출되는 위치에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="5e12d-307">자세한 내용은 [레이아웃 페이지](xref:mvc/views/layout)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="5e12d-308">[Layout](xref:mvc/views/layout#specifying-a-layout) 속성은 *Pages/_ViewStart.cshtml* 에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="5e12d-309">레이아웃은 *Pages/Shared* 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="5e12d-310">페이지는 현재 페이지와 동일한 폴더에서부터 시작하여 계층적으로 다른 뷰들(레이아웃, 템플릿, 부분 뷰)을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="5e12d-311">*Pages/Shared* 폴더의 레이아웃은 *Pages* 폴더 하위의 모든 Razor 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="5e12d-312">레이아웃 파일은 *Pages/Shared* 폴더에 위치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="5e12d-313">레이아웃 파일은 *Views/Shared* 폴더에 두지 **않는** 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="5e12d-314">*Views/Shared* 는 MVC 뷰 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="5e12d-315">Razor Pages는 경로 규칙이 아닌 폴더 계층 구조를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="5e12d-316">Razor Page의 뷰 검색에는 *Pages* 폴더가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="5e12d-317">MVC 컨트롤러 및 기존 Razor 뷰에서 사용한 레이아웃, 템플릿 및 부분 뷰는 ‘정상적으로 작동’합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="5e12d-318">*Pages/_ViewImports.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="5e12d-319">`@namespace`는 잠시 뒤에 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="5e12d-320">`@addTagHelper` 지시문은 [기본 제공 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/Index)를 *Pages* 폴더의 모든 페이지에 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="5e12d-321">페이지에서 설정된 `@namespace` 지시문:</span><span class="sxs-lookup"><span data-stu-id="5e12d-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="5e12d-322">`@namespace` 지시문은 페이지에 대한 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="5e12d-323">이렇게 하면 `@model` 지시문은 네임스페이스를 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="5e12d-324">`@namespace` 지시문이 *_ViewImports.cshtml* 에 포함되어 있으면 지정된 네임스페이스가 `@namespace` 지시문을 가져오는 페이지에서 생성된 네임스페이스에 대한 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="5e12d-325">생성된 네임스페이스의 나머지 부분(접미사 부분)은 *_ViewImports.cshtml* 이 위치한 폴더와 페이지가 위치한 폴더 간의 점으로 구분된 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="5e12d-326">예를 들어 `PageModel` 클래스 *Pages/Customers/Edit.cshtml.cs* 는 네임스페이스를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="5e12d-327">*Pages/_ViewImports.cshtml* 파일에서는 다음과 같이 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="5e12d-328">*Pages/Customers/Edit.cshtml* Razor Page에 대해 생성되는 네임스페이스는 `PageModel` 클래스와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="5e12d-329">`@namespace`는 ‘기존 Razor 뷰에서도 작동’합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="5e12d-330">*Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="5e12d-331">*_ViewImports.cshtml* 및 앞의 레이아웃 파일로 업데이트된 *Pages/Create.cshtml* 뷰 파일:</span><span class="sxs-lookup"><span data-stu-id="5e12d-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="5e12d-332">앞의 코드에서 *_ViewImports.cshtml* 은 네임스페이스 및 태그 도우미를 가져왔습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="5e12d-333">레이아웃 파일은 JavaScript 파일을 가져왔습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="5e12d-334">[Razor Pages 시작 프로젝트](#rpvs17)에는 클라이언트 측 유효성 검사를 연결하는 *Pages/_ValidationScriptsPartial.cshtml* 이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="5e12d-335">부분 뷰에 대한 자세한 내용은 <xref:mvc/views/partial>를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="5e12d-336">페이지에 대한 URL 생성하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-336">URL generation for Pages</span></span>

<span data-ttu-id="5e12d-337">앞에서 살펴본 `Create` 페이지는 `RedirectToPage`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="5e12d-338">예제 앱은 다음과 같은 파일/폴더 구조를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="5e12d-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="5e12d-339">*/Pages*</span></span>

  * <span data-ttu-id="5e12d-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="5e12d-341">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="5e12d-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="5e12d-342">*/Customers*</span></span>

    * <span data-ttu-id="5e12d-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="5e12d-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="5e12d-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-345">*Index.cshtml*</span></span>

<span data-ttu-id="5e12d-346">*Pages/Customers/Create.cshtml* 및 *Pages/Customers/Edit.cshtml* 페이지는 정상적으로 작업을 마친 후 *Pages/Customers/Index.cshtml* 로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="5e12d-347">문자열 `./Index`는 이전 페이지에 액세스하는 데 사용되는 상대 페이지 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="5e12d-348">이것은 *Pages/Customers/Index.cshtml* 페이지에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="5e12d-349">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5e12d-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="5e12d-350">절대 페이지 이름 `/Index`는 *Pages/Index.cshtml* 페이지에 대한 URL을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="5e12d-351">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5e12d-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="5e12d-352">페이지 이름은 선행 `/`를 포함하는 루트 */Pages* 폴더로부터 시작되는 페이지에 대한 경로입니다(예: `/Index`).</span><span class="sxs-lookup"><span data-stu-id="5e12d-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="5e12d-353">위의 URL 생성 예제는 URL 하드 코딩에 비해 향상된 옵션과 기능적 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="5e12d-354">URL 생성에는 [라우팅](xref:mvc/controllers/routing)이 사용되며 경로가 대상 경로에서 정의되는 방식에 따라 매개 변수를 생성하고 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="5e12d-355">페이지에 대한 URL 생성은 상대적 이름을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="5e12d-356">다음 표는 *Pages/Customers/Create.cshtml* 에서 다른 `RedirectToPage` 매개 변수를 사용할 때 어떤 인덱스 페이지가 선택되는지를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="5e12d-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="5e12d-357">RedirectToPage(x)</span></span>| <span data-ttu-id="5e12d-358">페이지</span><span class="sxs-lookup"><span data-stu-id="5e12d-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5e12d-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="5e12d-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-360">*Pages/Index*</span></span> |
| <span data-ttu-id="5e12d-361">RedirectToPage("./Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="5e12d-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="5e12d-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="5e12d-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-364">*Pages/Index*</span></span> |
| <span data-ttu-id="5e12d-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="5e12d-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="5e12d-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` 및 `RedirectToPage("../Index")`는 *상대적 이름* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="5e12d-368">`RedirectToPage`의 매개 변수는 현재 페이지의 경로와 *결합* 되어 대상 페이지의 이름을 컴퓨팅합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="5e12d-369">상대적 이름 연결은 구조가 복잡한 사이트를 만들때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="5e12d-370">상대적 이름을 사용하여 폴더의 페이지 간을 연결하는 경우:</span><span class="sxs-lookup"><span data-stu-id="5e12d-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="5e12d-371">폴더의 이름을 바꾸면 상대적 링크는 중단되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="5e12d-372">링크는 폴더 이름을 포함하지 않으므로 손상되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="5e12d-373">다른 [영역](xref:mvc/controllers/areas)에서 페이지로 리디렉션하려면 영역을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="5e12d-374">자세한 내용은 <xref:mvc/controllers/areas> 및 <xref:razor-pages/razor-pages-conventions>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="5e12d-375">ViewData 특성</span><span class="sxs-lookup"><span data-stu-id="5e12d-375">ViewData attribute</span></span>

<span data-ttu-id="5e12d-376"><xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>를 사용하여 데이터를 페이지에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="5e12d-377">`[ViewData]` 특성을 가진 속성은 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>에서 저장되고 로드된 값을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="5e12d-378">다음 예제에서 `AboutModel`은 `[ViewData]` 특성을 `Title` 속성에 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="5e12d-379">정보 페이지에서는 모델의 속성으로 `Title` 속성에 접근합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="5e12d-380">레이아웃에서는 ViewData 사전으로부터 이 제목을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="5e12d-381">TempData</span><span class="sxs-lookup"><span data-stu-id="5e12d-381">TempData</span></span>

<span data-ttu-id="5e12d-382">ASP.NET Core는 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="5e12d-383">이 속성은 해당 속성이 읽혀질 때까지만 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-383">This property stores data until it's read.</span></span> <span data-ttu-id="5e12d-384"><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> 및 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> 메서드를 사용하면 삭제하지 않고도 데이터를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="5e12d-385">`TempData`는 데이터가 둘 이상의 요청에 필요한 경우 리디렉션에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="5e12d-386">다음 코드는 `TempData`를 사용하여 `Message`의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="5e12d-387">*Pages/Customers/Index.cshtml* 파일의 다음 태그는 `TempData`를 사용하여 `Message` 값을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="5e12d-388">*Pages/Customers/Index.cshtml.cs* 페이지 모델은 `Message` 속성에 `[TempData]` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="5e12d-389">자세한 내용은 [TempData](xref:fundamentals/app-state#tempdata)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="5e12d-390">한 페이지에 대한 여러 처리기</span><span class="sxs-lookup"><span data-stu-id="5e12d-390">Multiple handlers per page</span></span>

<span data-ttu-id="5e12d-391">다음 페이지는 `asp-page-handler` 태그 도우미를 사용하여 두 처리기에 대한 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="5e12d-392">위 예제의 양식에는 두 개의 제출 단추가 존재하며, 각 단추는 `FormActionTagHelper`를 사용하여 서로 다른 URL로 제출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="5e12d-393">`asp-page-handler` 특성은 `asp-page`와 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="5e12d-394">`asp-page-handler`는 페이지에 정의된 각 처리기 메서드로 제출되는 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="5e12d-395">이 예제의 경우 현재 페이지로 연결므로 `asp-page`는 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="5e12d-396">페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="5e12d-397">이 코드는 *명명된 처리기 메서드* 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="5e12d-398">명명된 처리기 메서드는 `On<HTTP Verb>` 뒤와 `Async`(있는 경우) 앞에 이름의 텍스트를 결합해서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="5e12d-399">위의 예제에서 페이지 메서드는 OnPost **JoinList** Async와 OnPost **JoinListUC** Async입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="5e12d-400">*OnPost* 및 *Async* 가 제거된 처리기 이름은 `JoinList`와 `JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="5e12d-401">위의 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="5e12d-402">`OnPostJoinListUCAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="5e12d-403">사용자 지정 경로</span><span class="sxs-lookup"><span data-stu-id="5e12d-403">Custom routes</span></span>

<span data-ttu-id="5e12d-404">`@page` 지시문을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="5e12d-405">페이지에 대한 사용자 지정 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-405">Specify a custom route to a page.</span></span> <span data-ttu-id="5e12d-406">예를 들어 `@page "/Some/Other/Path"`를 사용하여 About 페이지에 대한 경로를 `/Some/Other/Path`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="5e12d-407">페이지의 기본 경로에 세그먼트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-407">Append segments to a page's default route.</span></span> <span data-ttu-id="5e12d-408">예를 들어 `@page "item"`을 사용하여 페이지의 기본 경로에 "item" 세그먼트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="5e12d-409">페이지의 기본 경로에 매개 변수를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="5e12d-410">예를 들어 `@page "{id}"`를 사용하여 ID 매개 변수 `id`를 페이지에 필수로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="5e12d-411">경로의 시작 부분에 물결표(`~`)로 지정된 루트 상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="5e12d-412">예를 들어 `@page "~/Some/Other/Path"`은 `@page "/Some/Other/Path"`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="5e12d-413">URL에서 쿼리 문자열 `?handler=JoinList`를 사용하지 않으려면 경로를 변경하여 처리기 이름을 URL의 경로 부분에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="5e12d-414">`@page` 지시문 뒤에 큰따옴표로 묶은 경로 템플릿을 추가하여 경로를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="5e12d-415">위의 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH/JoinList`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="5e12d-416">`OnPostJoinListUCAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH/JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="5e12d-417">`handler` 뒤의 `?`는 경로 매개 변수가 선택 사항임을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="5e12d-418">고급 구성 및 설정</span><span class="sxs-lookup"><span data-stu-id="5e12d-418">Advanced configuration and settings</span></span>

<span data-ttu-id="5e12d-419">다음 섹션의 구성 및 설정은 대부분의 앱에서 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="5e12d-420">고급 옵션을 구성하려면 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>를 구성하는 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 오버로드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="5e12d-421"><xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>를 사용하여 페이지의 루트 디렉터리를 설정하거나 페이지에 대한 애플리케이션 모델 규칙을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="5e12d-422">규칙에 대한 자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="5e12d-423">뷰를 미리 컴파일하려면 [Razor 뷰 컴파일](xref:mvc/views/view-compilation)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="5e12d-424">Razor Pages가 컨텐츠 루트에 있도록 지정</span><span class="sxs-lookup"><span data-stu-id="5e12d-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="5e12d-425">기본적으로 Razor Pages의 루트 경로는 */Pages* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="5e12d-426"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*>를 추가하여 Razor Pages가 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)(<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>)가 되도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="5e12d-427">Razor Pages가 사용자 지정 루트 디렉터리에 있도록 지정</span><span class="sxs-lookup"><span data-stu-id="5e12d-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="5e12d-428"><xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*>를 추가하여 Razor Pages가 앱의 사용자 지정 루트 디렉터리에 있도록 지정합니다(상대 경로 제공).</span><span class="sxs-lookup"><span data-stu-id="5e12d-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5e12d-429">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5e12d-429">Additional resources</span></span>

* <span data-ttu-id="5e12d-430">본문의 소개에 따라 빌드되는 [Razor Pages 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="5e12d-431">특성 및 Razor Pages 권한 부여</span><span class="sxs-lookup"><span data-stu-id="5e12d-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="5e12d-432">예제 코드 살펴보기 및 다운로드</span><span class="sxs-lookup"><span data-stu-id="5e12d-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="5e12d-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e12d-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e12d-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e12d-435">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="5e12d-436">Razor Pages 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="5e12d-436">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e12d-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-437">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e12d-438">Razor Pages 프로젝트를 만드는 방법에 대한 자세한 내용은 [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-438">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5e12d-439">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e12d-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5e12d-440">명령줄에서 `dotnet new webapp`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-440">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="5e12d-441">Mac용 Visual Studio에서 생성된 *.csproj* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-441">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5e12d-442">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e12d-442">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5e12d-443">명령줄에서 `dotnet new webapp`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-443">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="5e12d-444">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5e12d-444">Razor Pages</span></span>

<span data-ttu-id="5e12d-445">Razor Pages는 *Startup.cs* 에서 사용할 수 있게 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-445">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="5e12d-446">기본적인 페이지를 살펴보겠습니다. <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="5e12d-446">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="5e12d-447">앞의 코드는 컨트롤러 및 뷰가 포함된 ASP.NET Core 앱에서 사용되는 [Razor 뷰 파일](xref:tutorials/first-mvc-app/adding-view)과 매우 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-447">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="5e12d-448">차이점은 `@page` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-448">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="5e12d-449">`@page`는 파일을 MVC 액션으로 만드는데, 이 말은 컨트롤러를 거치지 않고 이 파일이 요청을 직접 처리한다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-449">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="5e12d-450">`@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-450">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="5e12d-451">`@page`는 다른 Razor 구문의 동작에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-451">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="5e12d-452">다음 두 파일은 `PageModel` 클래스를 사용하는 비슷한 페이지를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-452">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="5e12d-453">*Pages/Index2.cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-453">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="5e12d-454">*Pages/Index2.cshtml.cs* 페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-454">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="5e12d-455">규칙에 따라 `PageModel` 클래스 파일의 이름은 *.cs* 가 추가된 Razor Page 파일의 이름과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-455">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="5e12d-456">예를 들어 위의 Razor Page는 *Pages/Index2.cshtml* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-456">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="5e12d-457">그리고 `PageModel` 클래스가 포함된 파일의 이름은 *Pages/Index2.cshtml.cs* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-457">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="5e12d-458">페이지에 대한 URL 경로 연결은 파일 시스템 상의 페이지 위치에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-458">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="5e12d-459">다음 표는 Razor Page 경로 및 그와 일치하는 URL을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-459">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="5e12d-460">파일 이름 및 경로</span><span class="sxs-lookup"><span data-stu-id="5e12d-460">File name and path</span></span>               | <span data-ttu-id="5e12d-461">일치하는 URL</span><span class="sxs-lookup"><span data-stu-id="5e12d-461">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5e12d-462">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-462">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="5e12d-463">`/` 또는 `/Index`</span><span class="sxs-lookup"><span data-stu-id="5e12d-463">`/` or `/Index`</span></span> |
| <span data-ttu-id="5e12d-464">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-464">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="5e12d-465">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-465">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="5e12d-466">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-466">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="5e12d-467">`/Store` 또는 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="5e12d-467">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="5e12d-468">메모:</span><span class="sxs-lookup"><span data-stu-id="5e12d-468">Notes:</span></span>

* <span data-ttu-id="5e12d-469">런타임은 기본적으로 *Pages* 폴더에서 Razor Pages 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-469">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="5e12d-470">URL에 페이지가 지정되어 있지 않을 경우 `Index`가 기본 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-470">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="5e12d-471">기본적인 양식 작성하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-471">Write a basic form</span></span>

<span data-ttu-id="5e12d-472">Razor Pages는 앱을 만들 때 웹 브라우저에서 사용되는 일반적인 패턴을 손쉽게 구현할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-472">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="5e12d-473">[모델 바인딩](xref:mvc/models/model-binding), [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 HTML 도우미는 모두 Razor Page 클래스에 정의된 속성을 통해서 ‘정확하게 작동’합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-473">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="5e12d-474">`Contact` 모델에 대한 기본적인 "연락처" 양식을 구현하는 페이지를 생각해보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-474">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="5e12d-475">이 문서의 예제에서 `DbContext`는 [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 파일에서 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-475">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="5e12d-476">데이터 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-476">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="5e12d-477">db 컨텍스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-477">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="5e12d-478">*Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-478">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="5e12d-479">*Pages/Create.cshtml.cs* 페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-479">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="5e12d-480">규약에 따라 `PageModel` 클래스의 이름은 `<PageName>Model`로 지정하며 페이지와 동일한 네임스페이스에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-480">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="5e12d-481">`PageModel` 클래스를 사용하면 페이지의 논리를 페이지의 표현으로부터 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-481">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="5e12d-482">이 클래스는 페이지로 전송된 요청에 대한 페이지 처리기와 페이지를 렌더링하기 위해 사용되는 데이터를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-482">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="5e12d-483">이러한 분리를 통해 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-483">This separation allows:</span></span>

* <span data-ttu-id="5e12d-484">[종속성 주입](xref:fundamentals/dependency-injection)을 통해 페이지 종속성 관리</span><span class="sxs-lookup"><span data-stu-id="5e12d-484">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="5e12d-485">페이지 [유닛 테스트](xref:test/razor-pages-tests).</span><span class="sxs-lookup"><span data-stu-id="5e12d-485">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="5e12d-486">이 페이지에는 `POST` 요청 시(사용자가 양식을 게시할 때) 실행되는 `OnPostAsync` *처리기 메서드* 가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-486">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="5e12d-487">모든 HTTP 동사에 대한 처리기 메서드를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-487">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="5e12d-488">가장 일반적인 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-488">The most common handlers are:</span></span>

* <span data-ttu-id="5e12d-489">`OnGet`: 페이지에 필요한 상태를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-489">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="5e12d-490">[OnGet](#OnGet) 예제.</span><span class="sxs-lookup"><span data-stu-id="5e12d-490">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="5e12d-491">`OnPost`: 양식 제출을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-491">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="5e12d-492">`Async` 명명 접미사는 선택 사항이지만 비동기 함수에 대한 규약으로 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-492">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="5e12d-493">위의 코드는 Razor Pages의 일반적인 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-493">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="5e12d-494">컨트롤러 및 뷰를 사용하는 ASP.NET 앱에 대해 잘 알고 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="5e12d-494">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="5e12d-495">앞 예제의 `OnPostAsync` 코드는 일반적인 컨트롤러 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-495">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="5e12d-496">[모델 바인딩](xref:mvc/models/model-binding), [유효성 검사](xref:mvc/models/validation), [유효성 검사](xref:mvc/models/validation) 및 작업 결과 같은 MVC의 기본적인 기능들이 대부분 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-496">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="5e12d-497">기존 `OnPostAsync` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-497">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="5e12d-498">그리고 `OnPostAsync`의 기본적인 흐름은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-498">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="5e12d-499">유효성 검사 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-499">Check for validation errors.</span></span>

* <span data-ttu-id="5e12d-500">오류가 없는 경우 데이터를 저장하고 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-500">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="5e12d-501">오류가 있을 경우 유효성 검사 메시지가 포함된 페이지를 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-501">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="5e12d-502">클라이언트 측 유효성 검사는 기존의 ASP.NET Core MVC 애플리케이션과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-502">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="5e12d-503">대부분의 경우 유효성 검사 오류는 클라이언트에서 감지되어 서버에는 제출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-503">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="5e12d-504">데이터가 성공적으로 입력되면 `OnPostAsync` 처리기 메서드가 `RedirectToPage` 도우미 메서드를 호출하여 `RedirectToPageResult`의 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-504">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="5e12d-505">`RedirectToPage`는 `RedirectToAction`이나 `RedirectToRoute`와 비슷하지만 페이지에 맞춰진 새로운 액션 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-505">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="5e12d-506">위의 예제에서 이 메서드는 루트 인덱스 페이지(`/Index`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-506">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="5e12d-507">`RedirectToPage`는 [페이지에 대한 URL 생성](#url_gen) 섹션에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-507">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="5e12d-508">서버로 전달된 제출 양식에 유효성 검사 오류가 존재할 경우 `OnPostAsync` 처리기 메서드가 `Page` 도우미 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-508">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="5e12d-509">`Page`는 `PageResult`의 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-509">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="5e12d-510">`Page`를 반환하는 것은 컨트롤의 액션에서 `View`를 반환하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-510">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="5e12d-511">`PageResult`는 처리기 메서드의 기본 반환 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-511">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="5e12d-512">`void`를 반환하는 처리기 메서드는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-512">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="5e12d-513">`Customer` 속성은 `[BindProperty]` 특성을 이용해서 모델 바인딩 대상으로 명시적으로 지정(opt in)합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-513">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="5e12d-514">Razor Pages는 기본적으로 비 `GET` 동사에 대해서만 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-514">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="5e12d-515">속성을 바인딩하면 작성해야 하는 코드의 양을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-515">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="5e12d-516">바인딩은 양식 필드 렌더링할 때와 (`<input asp-for="Customer.Name">`) 입력을 받아들일 때 동일한 속성을 사용하여 코드를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-516">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="5e12d-517">홈페이지(*Index.cshtml*)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-517">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="5e12d-518">연결된 `PageModel` 클래스(*Index.cshtml.cs*)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-518">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="5e12d-519">*Index.cshtml* 파일에는 각 연락처에 대한 편집 링크를 만들기 위한 다음의 태그가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-519">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="5e12d-520">이 `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)는 편집 페이지에 대한 링크를 생성하기 위해 `asp-route-{value}` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-520">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="5e12d-521">링크에는 연락처 ID와 함께 경로 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-521">The link contains route data with the contact ID.</span></span> <span data-ttu-id="5e12d-522">예: `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="5e12d-522">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="5e12d-523">[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-523">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="5e12d-524">태그 도우미는 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`를 통해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-524">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="5e12d-525">*Pages/Edit.cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-525">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="5e12d-526">첫 번째 줄에는 `@page "{id:int}"` 지시문이 작성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-526">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="5e12d-527">라우팅 제약 조건인 `"{id:int}"`는 `int` 경로 데이터가 포함된 이 페이지에 대한 요청만 허용하도록 페이지에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-527">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="5e12d-528">페이지에 대한 요청에 `int`로 변환할 수 있는 경로 데이터가 없으면 런타임이 HTTP 404 (찾을 수 없음) 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-528">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="5e12d-529">ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-529">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="5e12d-530">*Pages/Edit.cshtml.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-530">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="5e12d-531">또한 *Index.cshtml* 파일에는 각 고객 연락처에 대한 삭제 버튼을 만들기 위한 태그가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-531">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="5e12d-532">삭제 단추가 HTML로 렌더링될 때 해당 태그의 `formaction`에는 다음에 관한 매개 변수가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-532">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="5e12d-533">`asp-route-id` 특성으로 지정된 고객 연락처의 ID.</span><span class="sxs-lookup"><span data-stu-id="5e12d-533">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="5e12d-534">`asp-page-handler` 특성으로 지정된 `handler`</span><span class="sxs-lookup"><span data-stu-id="5e12d-534">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="5e12d-535">고객 연락처 ID `1`에 대해 렌더링된 삭제 단추의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-535">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="5e12d-536">단추를 선택하면 양식의 `POST` 요청이 서버로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-536">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="5e12d-537">규약에 따라 처리기 메서드의 이름은 `handler` 매개 변수의 값을 기반으로 `OnPost[handler]Async` 체계에 의해 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-537">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="5e12d-538">이번 예제에서는 `handler`가 `delete`이므로 `POST` 요청을 처리하기 위해 `OnPostDeleteAsync` 처리기 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-538">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="5e12d-539">`asp-page-handler`가 `remove` 같은 다른 값으로 설정되면 `OnPostRemoveAsync`라는 이름의 처리기 메서드가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-539">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="5e12d-540">다음은 `OnPostDeleteAsync` 처리기를 보여 주는 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-540">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="5e12d-541">`OnPostDeleteAsync` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-541">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="5e12d-542">쿼리 문자열에서 `id`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-542">Accepts the `id` from the query string.</span></span> <span data-ttu-id="5e12d-543">*Index.cshtml* 페이지 지시문에 라우팅 제약 조건 `"{id:int?}"`이 포함된 경우 `id`는 경로 데이터에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-543">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="5e12d-544">`id`의 경로 데이터는 `https://localhost:5001/Customers/2`와 같은 URI에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-544">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="5e12d-545">`FindAsync`를 사용하여 데이터베이스에서 고객 연락처를 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-545">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="5e12d-546">고객 연락처를 찾으면 고객 연락처 목록에서 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-546">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="5e12d-547">데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-547">The database is updated.</span></span>
* <span data-ttu-id="5e12d-548">`RedirectToPage`를 호출하여 루트 인덱스 페이지(`/Index`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-548">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="5e12d-549">페이지 속성을 필수로 표시하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-549">Mark page properties as required</span></span>

<span data-ttu-id="5e12d-550">`PageModel`의 속성에는 [필수](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-550">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="5e12d-551">자세한 내용은 [모델 유효성 검사](xref:mvc/models/validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-551">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="5e12d-552">OnGet 처리기 대체를 사용하여 HEAD 요청 처리</span><span class="sxs-lookup"><span data-stu-id="5e12d-552">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="5e12d-553">`HEAD` 요청을 사용하면 특정 리소스의 헤더를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-553">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="5e12d-554">`GET` 요청과는 달리 `HEAD` 요청은 응답 본문을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-554">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="5e12d-555">일반적으로 `HEAD` 요청에 대한 `OnHead` 처리기를 만들고 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-555">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="5e12d-556">만약 정의된 `OnHead` 처리기가 없다면 ASP.NET Core 2.1 이상에서는 Razor Pages가 `OnGet` 처리기 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-556">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="5e12d-557">이 동작은 `Startup.ConfigureServices`의 [SetCompatibilityVersion](xref:mvc/compatibility-version)에 대한 호출에 의해 사용 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-557">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="5e12d-558">기본 템플릿은 ASP.NET Core 2.1 및 2.2에서 `SetCompatibilityVersion` 호출을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-558">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="5e12d-559">`SetCompatibilityVersion`은 효과적으로 Razor Pages 옵션 `AllowMappingHeadRequestsToGetHandler`를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-559">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="5e12d-560">`SetCompatibilityVersion`을 사용하여 모든 동작을 허용하는 대신 명시적으로 *특정* 동작을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-560">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="5e12d-561">다음 코드는 `HEAD` 요청을 `OnGet` 처리기에 매핑할 수 있도록 옵트인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-561">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="5e12d-562">XSRF/CSRF 및 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5e12d-562">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="5e12d-563">[위조 방지 유효성 검사](xref:security/anti-request-forgery)에 대한 어떠한 코드도 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-563">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="5e12d-564">위조 방지 토큰 생성 및 유효성 검사는 Razor Pages에 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-564">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="5e12d-565">Razor Pages에서 레이아웃, 부분 뷰, 템플릿 및 태그 도우미 사용하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-565">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="5e12d-566">Pages는 Razor 뷰 엔진의 모든 기능과 함께 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-566">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="5e12d-567">레이아웃, 부분 뷰, 템플릿, 태그 도우미, *_ViewStart.cshtml*, *_ViewImports.cshtml* 은 기존의 Razor 뷰와 동일한 방식으로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-567">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="5e12d-568">이 기능들 중 일부를 활용하여 페이지를 개선해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-568">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="5e12d-569">[레이아웃 페이지](xref:mvc/views/layout)를 *Pages/Shared/_Layout.cshtml* 에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-569">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="5e12d-570">[레이아웃](xref:mvc/views/layout)은 다음과 같은 역할을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-570">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="5e12d-571">각 페이지의 레이아웃을 제어합니다(페이지가 명시적으로 레이아웃을 사용하지 않는 경우 제외).</span><span class="sxs-lookup"><span data-stu-id="5e12d-571">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="5e12d-572">JavaScript 및 스타일시트 같은 HTML 구조를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-572">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="5e12d-573">자세한 내용은 [레이아웃 페이지](xref:mvc/views/layout)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-573">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="5e12d-574">[Layout](xref:mvc/views/layout#specifying-a-layout) 속성은 *Pages/_ViewStart.cshtml* 에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-574">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="5e12d-575">레이아웃은 *Pages/Shared* 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-575">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="5e12d-576">페이지는 현재 페이지와 동일한 폴더에서부터 시작하여 계층적으로 다른 뷰들(레이아웃, 템플릿, 부분 뷰)을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-576">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="5e12d-577">*Pages/Shared* 폴더의 레이아웃은 *Pages* 폴더 하위의 모든 Razor 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-577">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="5e12d-578">레이아웃 파일은 *Pages/Shared* 폴더에 위치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-578">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="5e12d-579">레이아웃 파일은 *Views/Shared* 폴더에 두지 **않는** 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-579">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="5e12d-580">*Views/Shared* 는 MVC 뷰 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-580">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="5e12d-581">Razor Pages는 경로 규칙이 아닌 폴더 계층 구조를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-581">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="5e12d-582">Razor Page의 뷰 검색에는 *Pages* 폴더가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-582">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="5e12d-583">MVC 컨트롤러 및 기존 Razor 뷰에서 사용 중인 레이아웃, 템플릿 및 부분 뷰는 ‘정상적으로 작동’합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-583">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="5e12d-584">*Pages/_ViewImports.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-584">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="5e12d-585">`@namespace`는 잠시 뒤에 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-585">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="5e12d-586">`@addTagHelper` 지시문은 [기본 제공 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/Index)를 *Pages* 폴더의 모든 페이지에 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-586">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="5e12d-587">`@namespace` 지시문을 페이지에서 명시적으로 사용하면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-587">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="5e12d-588">이 지시문은 페이지에 대한 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-588">The directive sets the namespace for the page.</span></span> <span data-ttu-id="5e12d-589">이렇게 하면 `@model` 지시문은 네임스페이스를 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-589">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="5e12d-590">`@namespace` 지시문이 *_ViewImports.cshtml* 에 포함되어 있으면 지정된 네임스페이스가 `@namespace` 지시문을 가져오는 페이지에서 생성된 네임스페이스에 대한 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-590">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="5e12d-591">생성된 네임스페이스의 나머지 부분(접미사 부분)은 *_ViewImports.cshtml* 이 위치한 폴더와 페이지가 위치한 폴더 간의 점으로 구분된 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-591">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="5e12d-592">예를 들어 `PageModel` 클래스 *Pages/Customers/Edit.cshtml.cs* 는 네임스페이스를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-592">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="5e12d-593">*Pages/_ViewImports.cshtml* 파일에서는 다음과 같이 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-593">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="5e12d-594">*Pages/Customers/Edit.cshtml* Razor Page에 대해 생성되는 네임스페이스는 `PageModel` 클래스와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-594">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="5e12d-595">`@namespace`는 ‘기존 Razor 뷰에서도 작동’합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-595">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="5e12d-596">기존의 *Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-596">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="5e12d-597">변경된 *Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-597">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="5e12d-598">[Razor Pages 시작 프로젝트](#rpvs17)에는 클라이언트 측 유효성 검사를 연결하는 *Pages/_ValidationScriptsPartial.cshtml* 이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-598">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="5e12d-599">부분 뷰에 대한 자세한 내용은 <xref:mvc/views/partial>를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-599">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="5e12d-600">페이지에 대한 URL 생성하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-600">URL generation for Pages</span></span>

<span data-ttu-id="5e12d-601">앞에서 살펴본 `Create` 페이지는 `RedirectToPage`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-601">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="5e12d-602">예제 앱은 다음과 같은 파일/폴더 구조를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-602">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="5e12d-603">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="5e12d-603">*/Pages*</span></span>

  * <span data-ttu-id="5e12d-604">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-604">*Index.cshtml*</span></span>
  * <span data-ttu-id="5e12d-605">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="5e12d-605">*/Customers*</span></span>

    * <span data-ttu-id="5e12d-606">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-606">*Create.cshtml*</span></span>
    * <span data-ttu-id="5e12d-607">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-607">*Edit.cshtml*</span></span>
    * <span data-ttu-id="5e12d-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5e12d-608">*Index.cshtml*</span></span>

<span data-ttu-id="5e12d-609">*Pages/Customers/Create.cshtml* 및 *Pages/Customers/Edit.cshtml* 페이지는 정상적으로 작업을 마친 후 *Pages/Index.cshtml* 로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-609">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="5e12d-610">문자열 `/Index`는 이전 페이지에 접근하기 위한 URI의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-610">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="5e12d-611">문자열 `/Index`는 *Pages/Index.cshtml* 페이지에 대한 URI를 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-611">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="5e12d-612">예들 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-612">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="5e12d-613">페이지 이름은 선행 `/`를 포함하는 루트 */Pages* 폴더로부터 시작되는 페이지에 대한 경로입니다(예: `/Index`).</span><span class="sxs-lookup"><span data-stu-id="5e12d-613">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="5e12d-614">위의 URL 생성 예제는 URL 하드 코딩에 비해 향상된 옵션과 기능적 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-614">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="5e12d-615">URL 생성에는 [라우팅](xref:mvc/controllers/routing)이 사용되며 경로가 대상 경로에서 정의되는 방식에 따라 매개 변수를 생성하고 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-615">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="5e12d-616">페이지에 대한 URL 생성은 상대적 이름을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-616">URL generation for pages supports relative names.</span></span> <span data-ttu-id="5e12d-617">다음 표는 *Pages/Customers/Create.cshtml* 에서 다른 `RedirectToPage` 매개 변수를 사용할 때 어떤 인덱스 페이지가 선택되는지를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-617">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="5e12d-618">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="5e12d-618">RedirectToPage(x)</span></span>| <span data-ttu-id="5e12d-619">페이지</span><span class="sxs-lookup"><span data-stu-id="5e12d-619">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5e12d-620">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-620">RedirectToPage("/Index")</span></span> | <span data-ttu-id="5e12d-621">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-621">*Pages/Index*</span></span> |
| <span data-ttu-id="5e12d-622">RedirectToPage("./Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-622">RedirectToPage("./Index");</span></span> | <span data-ttu-id="5e12d-623">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-623">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="5e12d-624">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-624">RedirectToPage("../Index")</span></span> | <span data-ttu-id="5e12d-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-625">*Pages/Index*</span></span> |
| <span data-ttu-id="5e12d-626">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="5e12d-626">RedirectToPage("Index")</span></span>  | <span data-ttu-id="5e12d-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5e12d-627">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="5e12d-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")` 및 `RedirectToPage("../Index")`는 *상대적 이름* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="5e12d-629">`RedirectToPage`의 매개 변수는 현재 페이지의 경로와 *결합* 되어 대상 페이지의 이름을 컴퓨팅합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-629">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="5e12d-630">상대적 이름 연결은 구조가 복잡한 사이트를 만들때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-630">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="5e12d-631">상대적 이름을 사용하여 한 폴더의 여러 페이지들을 연결하면 해당 폴더의 이름을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-631">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="5e12d-632">그래도 여전히 모든 링크는 동작합니다(링크에 폴더 이름이 포함되어 있지 않기 때문입니다).</span><span class="sxs-lookup"><span data-stu-id="5e12d-632">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="5e12d-633">다른 [영역](xref:mvc/controllers/areas)에서 페이지로 리디렉션하려면 영역을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-633">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="5e12d-634">자세한 내용은 <xref:mvc/controllers/areas>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-634">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="5e12d-635">ViewData 특성</span><span class="sxs-lookup"><span data-stu-id="5e12d-635">ViewData attribute</span></span>

<span data-ttu-id="5e12d-636">[ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)를 사용하여 데이터를 페이지에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-636">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="5e12d-637">`[ViewData]` 속성이 있는 컨트롤러나 Razor Page 모델은 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)에 값이 저장되고 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-637">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="5e12d-638">다음 예제에서 `AboutModel`에는 `[ViewData]`가 표시된 `Title` 속성이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-638">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="5e12d-639">이 `Title` 속성은 About 페이지의 제목으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-639">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="5e12d-640">정보 페이지에서는 모델의 속성으로 `Title` 속성에 접근합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-640">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="5e12d-641">레이아웃에서는 ViewData 사전으로부터 이 제목을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-641">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="5e12d-642">TempData</span><span class="sxs-lookup"><span data-stu-id="5e12d-642">TempData</span></span>

<span data-ttu-id="5e12d-643">ASP.NET Core는 [컨트롤러](/dotnet/api/microsoft.aspnetcore.mvc.controller)에서 [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-643">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="5e12d-644">이 속성은 해당 속성이 읽혀질 때까지만 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-644">This property stores data until it's read.</span></span> <span data-ttu-id="5e12d-645">`Keep` 및 `Peek` 메서드를 사용하면 삭제하지 않고도 데이터를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-645">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="5e12d-646">`TempData`는 두 단계 이상의 요청에 대한 데이터가 필요할 경우의 리디렉션에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-646">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="5e12d-647">다음 코드는 `TempData`를 사용하여 `Message`의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-647">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="5e12d-648">*Pages/Customers/Index.cshtml* 파일의 다음 태그는 `TempData`를 사용하여 `Message` 값을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-648">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="5e12d-649">*Pages/Customers/Index.cshtml.cs* 페이지 모델은 `Message` 속성에 `[TempData]` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-649">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="5e12d-650">자세한 내용은 [TempData](xref:fundamentals/app-state#tempdata)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-650">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="5e12d-651">한 페이지에 대한 여러 처리기</span><span class="sxs-lookup"><span data-stu-id="5e12d-651">Multiple handlers per page</span></span>

<span data-ttu-id="5e12d-652">다음 페이지는 `asp-page-handler` 태그 도우미를 사용하여 두 처리기에 대한 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-652">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="5e12d-653">위 예제의 양식에는 두 개의 제출 단추가 존재하며, 각 단추는 `FormActionTagHelper`를 사용하여 서로 다른 URL로 제출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-653">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="5e12d-654">`asp-page-handler` 특성은 `asp-page`와 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-654">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="5e12d-655">`asp-page-handler`는 페이지에 정의된 각 처리기 메서드로 제출되는 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-655">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="5e12d-656">이 예제의 경우 현재 페이지로 연결므로 `asp-page`는 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-656">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="5e12d-657">페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-657">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="5e12d-658">이 코드는 *명명된 처리기 메서드* 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-658">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="5e12d-659">명명된 처리기 메서드는 `On<HTTP Verb>` 뒤와 `Async`(있는 경우) 앞에 이름의 텍스트를 결합해서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-659">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="5e12d-660">위의 예제에서 페이지 메서드는 OnPost **JoinList** Async와 OnPost **JoinListUC** Async입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-660">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="5e12d-661">*OnPost* 및 *Async* 가 제거된 처리기 이름은 `JoinList`와 `JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-661">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="5e12d-662">위의 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH?handler=JoinList`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-662">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="5e12d-663">`OnPostJoinListUCAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-663">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="5e12d-664">사용자 지정 경로</span><span class="sxs-lookup"><span data-stu-id="5e12d-664">Custom routes</span></span>

<span data-ttu-id="5e12d-665">`@page` 지시문을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-665">Use the `@page` directive to:</span></span>

* <span data-ttu-id="5e12d-666">페이지에 대한 사용자 지정 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-666">Specify a custom route to a page.</span></span> <span data-ttu-id="5e12d-667">예를 들어 `@page "/Some/Other/Path"`를 사용하여 About 페이지에 대한 경로를 `/Some/Other/Path`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-667">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="5e12d-668">페이지의 기본 경로에 세그먼트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-668">Append segments to a page's default route.</span></span> <span data-ttu-id="5e12d-669">예를 들어 `@page "item"`을 사용하여 페이지의 기본 경로에 "item" 세그먼트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-669">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="5e12d-670">페이지의 기본 경로에 매개 변수를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-670">Append parameters to a page's default route.</span></span> <span data-ttu-id="5e12d-671">예를 들어 `@page "{id}"`를 사용하여 ID 매개 변수 `id`를 페이지에 필수로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-671">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="5e12d-672">경로의 시작 부분에 물결표(`~`)로 지정된 루트 상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-672">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="5e12d-673">예를 들어 `@page "~/Some/Other/Path"`은 `@page "/Some/Other/Path"`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-673">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="5e12d-674">URL에서 쿼리 문자열 `?handler=JoinList`를 사용하지 않으려면 경로를 변경하여 처리기 이름을 URL의 경로 부분에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-674">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="5e12d-675">`@page` 지시문 뒤에 큰따옴표로 묶은 경로 템플릿을 추가하여 경로를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-675">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="5e12d-676">위의 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH/JoinList`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-676">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="5e12d-677">`OnPostJoinListUCAsync`에 제출되는 URL 경로는 `https://localhost:5001/Customers/CreateFATH/JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-677">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="5e12d-678">`handler` 뒤의 `?`는 경로 매개 변수가 선택 사항임을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-678">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="5e12d-679">구성 및 설정하기</span><span class="sxs-lookup"><span data-stu-id="5e12d-679">Configuration and settings</span></span>

<span data-ttu-id="5e12d-680">고급 옵션을 구성하려면 MVC 빌더에서 확장 메서드 `AddRazorPagesOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-680">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="5e12d-681">현재 `RazorPagesOptions`를 사용하여 페이지의 루트 디렉터리를 설정하거나 페이지에 대한 애플리케이션 모델 규칙을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-681">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="5e12d-682">앞으로 이 방식으로 더 많은 확장성을 지원할 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-682">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="5e12d-683">뷰를 미리 컴파일하려면 [Razor 뷰 컴파일](xref:mvc/views/view-compilation)을 참고하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-683">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="5e12d-684">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="5e12d-684">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="5e12d-685">본문의 소개에 따라 빌드되는 [Razor Pages 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e12d-685">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="5e12d-686">Razor Pages가 컨텐츠 루트에 있도록 지정</span><span class="sxs-lookup"><span data-stu-id="5e12d-686">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="5e12d-687">기본적으로 Razor Pages의 루트 경로는 */Pages* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-687">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="5e12d-688">[AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_)에 [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot)를 추가하여 Razor Pages가 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath))에 있도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e12d-688">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="5e12d-689">Razor Pages가 사용자 지정 루트 디렉터리에 있도록 지정</span><span class="sxs-lookup"><span data-stu-id="5e12d-689">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="5e12d-690">[AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_)에 [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot)를 추가하여 Razor Pages가 앱의 사용자 지정 루트 디렉터리에 있도록 지정합니다(상대 경로 제공).</span><span class="sxs-lookup"><span data-stu-id="5e12d-690">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="5e12d-691">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5e12d-691">Additional resources</span></span>

* [<span data-ttu-id="5e12d-692">특성 및 Razor Pages 권한 부여</span><span class="sxs-lookup"><span data-stu-id="5e12d-692">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
