---
title: 'Razor ASP.NET Core의 페이지 권한 부여 규칙'
author: rick-anderson
description: 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지 또는 페이지의 폴더에 액세스 하도록 허용 하는 규칙으로 페이지에 대 한 액세스를 제어 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 69e1d639aeb55ae64cc54b1cda402ed6bcbb04ab
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060185"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="7db76-103">Razor ASP.NET Core의 페이지 권한 부여 규칙</span><span class="sxs-lookup"><span data-stu-id="7db76-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7db76-104">페이지 앱에서 액세스를 제어 하는 한 가지 방법은 Razor 시작할 때 권한 부여 규칙을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="7db76-105">이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="7db76-106">이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="7db76-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7db76-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7db76-108">샘플 앱은 [ cookie 없이 ASP.NET Core Identity 인증 ](xref:security/authentication/cookie)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="7db76-109">이 항목에 표시 된 개념과 예제는를 사용 하는 앱에 동일 하 게 적용 ASP.NET Core Identity 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="7db76-110">를 사용 하려면 ASP.NET Core Identity 의 지침을 따르세요 <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="7db76-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="7db76-111">페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-111">Require authorization to access a page</span></span>

<span data-ttu-id="7db76-112">규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="7db76-113">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="7db76-114">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="7db76-115">는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="7db76-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="7db76-116">자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7db76-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="7db76-117">페이지의 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="7db76-118">규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="7db76-119">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="7db76-120">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="7db76-121">영역 페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-121">Require authorization to access an area page</span></span>

<span data-ttu-id="7db76-122">규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로의 영역 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="7db76-123">페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="7db76-124">예를 들어 파일 *영역/ Identity /Pages/Manage/Accounts.cshtml* 의 페이지 이름은 */Manage/Accounts* 입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="7db76-125">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="7db76-126">영역 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="7db76-127">규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 폴더의 모든 영역에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="7db76-128">폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="7db76-129">예를 들어 *영역/ Identity /Pages/Manage/* 아래에 있는 파일의 폴더 경로는/ *관리* 입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="7db76-130">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="7db76-131">페이지에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="7db76-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="7db76-132">규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="7db76-133">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="7db76-134">페이지의 폴더에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="7db76-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="7db76-135">규칙을 사용 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 하 여 지정 된 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="7db76-136">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="7db76-137">권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항</span><span class="sxs-lookup"><span data-stu-id="7db76-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="7db76-138">페이지의 폴더에 권한 부여가 필요 하다 고 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="7db76-139">그러나 반대의 경우는 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="7db76-140">익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="7db76-141">개인 페이지에 대 한 권한 부여가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="7db76-142"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>및 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지에 적용 되 면가 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 우선 하 고 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7db76-143">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7db76-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7db76-144">페이지 앱에서 액세스를 제어 하는 한 가지 방법은 Razor 시작할 때 권한 부여 규칙을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="7db76-145">이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="7db76-146">이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="7db76-147">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7db76-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7db76-148">샘플 앱은 [ cookie 없이 ASP.NET Core Identity 인증 ](xref:security/authentication/cookie)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="7db76-149">이 항목에 표시 된 개념과 예제는를 사용 하는 앱에 동일 하 게 적용 ASP.NET Core Identity 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="7db76-150">를 사용 하려면 ASP.NET Core Identity 의 지침을 따르세요 <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="7db76-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="7db76-151">페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-151">Require authorization to access a page</span></span>

<span data-ttu-id="7db76-152">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="7db76-153">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="7db76-154">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="7db76-155">는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="7db76-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="7db76-156">자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7db76-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="7db76-157">페이지의 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="7db76-158">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="7db76-159">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="7db76-160">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="7db76-161">영역 페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-161">Require authorization to access an area page</span></span>

<span data-ttu-id="7db76-162">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로의 영역 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="7db76-163">페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="7db76-164">예를 들어 파일 *영역/ Identity /Pages/Manage/Accounts.cshtml* 의 페이지 이름은 */Manage/Accounts* 입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="7db76-165">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="7db76-166">영역 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="7db76-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="7db76-167">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 경로에 있는 폴더의 모든 영역에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="7db76-168">폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="7db76-169">예를 들어 *영역/ Identity /Pages/Manage/* 아래에 있는 파일의 폴더 경로는/ *관리* 입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="7db76-170">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="7db76-171">페이지에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="7db76-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="7db76-172">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="7db76-173">지정 된 경로는 Razor 확장명이 없고 슬래시만 포함 하는 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="7db76-174">페이지의 폴더에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="7db76-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="7db76-175">를 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 통해 규칙을 사용 하 여 지정 된 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="7db76-176">지정 된 경로는 Razor 페이지 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="7db76-177">권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항</span><span class="sxs-lookup"><span data-stu-id="7db76-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="7db76-178">권한 부여가 필요한 페이지의 폴더를 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="7db76-179">그러나 반대의 경우는 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="7db76-180">익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="7db76-181">개인 페이지에 대 한 권한 부여가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="7db76-182"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>및 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지에 적용 되 면가 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 우선 하 고 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db76-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7db76-183">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="7db76-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
