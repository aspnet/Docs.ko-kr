---
title: ASP.NET Core의 응용 프로그램 부분과 컨트롤러, 뷰, 페이지 등을 공유 합니다. Razor
author: rick-anderson
description: RazorASP.NET Core의 응용 프로그램 부분을 사용 하 여 컨트롤러, 뷰, 페이지 등 공유
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 23bc1db6a184e7babe87e2d311a8ac4a59e78dd0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588362"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="02732-103">Razor응용 프로그램 파트를 사용 하 여 컨트롤러, 뷰, 페이지 등 공유</span><span class="sxs-lookup"><span data-stu-id="02732-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02732-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02732-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="02732-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02732-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="02732-106">*애플리케이션 파트* 는 앱의 리소스에 대한 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="02732-107">응용 프로그램 파트를 사용 하 여 컨트롤러 검색, 구성 요소 보기, 태그 도우미, Razor 페이지, razor 컴파일 소스 등을 ASP.NET Core 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="02732-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>는 애플리케이션 파트입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="02732-109">`AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="02732-110">[기능 공급자](#fp)는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="02732-111">애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="02732-112">예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="02732-113">응용 프로그램 파트를 사용 하 여 여러 앱에서 컨트롤러, 뷰, Razor 페이지, razor 컴파일 소스, 태그 도우미 등을 포함 하는 어셈블리 (DLL)를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="02732-114">여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="02732-115">ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="02732-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="02732-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="02732-117">ASP.NET Core 기능 로드</span><span class="sxs-lookup"><span data-stu-id="02732-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="02732-118"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 및 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="02732-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="02732-120">`ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="02732-121">다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="02732-122">위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="02732-123">`SharedController`는 앱의 프로젝트에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="02732-124">[WebAppParts 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) 샘플 다운로드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02732-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="02732-125">보기 포함</span><span class="sxs-lookup"><span data-stu-id="02732-125">Include views</span></span>

<span data-ttu-id="02732-126">[ Razor 클래스 라이브러리](xref:razor-pages/ui-class) 를 사용 하 여 어셈블리에 뷰를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="02732-127">리소스 로드 방지</span><span class="sxs-lookup"><span data-stu-id="02732-127">Prevent loading resources</span></span>

<span data-ttu-id="02732-128">애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지* 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="02732-129"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02732-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="02732-130">`ApplicationParts` 컬렉션에 있는 항목의 순서는 중요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="02732-131">컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="02732-132">예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="02732-133">`ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="02732-134">`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="02732-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="02732-135">앱의 어셈블리 및 종속 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="02732-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="02732-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="02732-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="02732-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="02732-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="02732-138">기능 공급자</span><span class="sxs-lookup"><span data-stu-id="02732-138">Feature providers</span></span>

<span data-ttu-id="02732-139">애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="02732-140">다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="02732-141">`internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="02732-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="02732-142">기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속됩니다. 여기서 `T`는 기능의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="02732-143">기능 공급자는 이전에 나열된 모든 기능 형식에 대해 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="02732-144">`ApplicationPartManager.FeatureProviders`의 기능 공급자 순서는 런타임 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="02732-145">나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="02732-146">사용 가능 기능 표시</span><span class="sxs-lookup"><span data-stu-id="02732-146">Display available features</span></span>

<span data-ttu-id="02732-147">앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="02732-148">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="02732-149">애플리케이션 파트에서 검색</span><span class="sxs-lookup"><span data-stu-id="02732-149">Discovery in application parts</span></span>

<span data-ttu-id="02732-150">HTTP 404 오류는 애플리케이션 파트를 사용하여 개발할 때 일반적이지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="02732-151">이러한 오류는 일반적으로 애플리케이션 파트를 검색하는 방법에 대한 필수 요구 사항이 없기 때문에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="02732-152">앱에서 HTTP 404 오류를 반환하는 경우 다음 요구 사항이 충족되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="02732-153">`applicationName` 설정은 검색에 사용되는 루트 어셈블리로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="02732-154">검색에 사용되는 루트 어셈블리는 일반적으로 진입점 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="02732-155">루트 어셈블리에는 검색에 사용되는 파트에 대한 참조가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="02732-156">참조는 직접적 또는 전이적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="02732-157">루트 어셈블리는 웹 SDK를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="02732-158">프레임워크에는 검색에 사용되는 루트 어셈블리에 특성을 스탬핑하는 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02732-159">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02732-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="02732-160">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02732-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="02732-161">*애플리케이션 파트* 는 앱의 리소스에 대한 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="02732-162">응용 프로그램 파트를 사용 하 여 컨트롤러 검색, 구성 요소 보기, 태그 도우미, Razor 페이지, razor 컴파일 소스 등을 ASP.NET Core 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="02732-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)는 애플리케이션 파트입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="02732-164">`AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="02732-165">*기능 공급자* 는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="02732-166">애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="02732-167">예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="02732-168">응용 프로그램 파트를 사용 하 여 여러 앱에서 컨트롤러, 뷰, Razor 페이지, razor 컴파일 소스, 태그 도우미 등을 포함 하는 어셈블리 (DLL)를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="02732-169">여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="02732-170">ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="02732-171"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="02732-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="02732-172">ASP.NET Core 기능 로드</span><span class="sxs-lookup"><span data-stu-id="02732-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="02732-173">`ApplicationPart` 및 `AssemblyPart` 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="02732-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="02732-175">`ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="02732-176">다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="02732-177">위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="02732-178">`SharedController`는 애플리케이션의 프로젝트에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="02732-179">[WebAppParts 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 샘플 다운로드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02732-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="02732-180">보기 포함</span><span class="sxs-lookup"><span data-stu-id="02732-180">Include views</span></span>

<span data-ttu-id="02732-181">[ Razor 클래스 라이브러리](xref:razor-pages/ui-class) 를 사용 하 여 어셈블리에 뷰를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="02732-182">리소스 로드 방지</span><span class="sxs-lookup"><span data-stu-id="02732-182">Prevent loading resources</span></span>

<span data-ttu-id="02732-183">애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지* 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="02732-184"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02732-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="02732-185">`ApplicationParts` 컬렉션에 있는 항목의 순서는 중요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="02732-186">컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="02732-187">예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="02732-188">`ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="02732-189">다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>를 사용하여 앱에서 `MyDependentLibrary`를 제거합니다.[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="02732-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="02732-190">`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="02732-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="02732-191">앱의 어셈블리 및 종속 어셈블리.</span><span class="sxs-lookup"><span data-stu-id="02732-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="02732-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="02732-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="02732-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="02732-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="02732-194">애플리케이션 기능 공급자</span><span class="sxs-lookup"><span data-stu-id="02732-194">Application feature providers</span></span>

<span data-ttu-id="02732-195">애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="02732-196">다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="02732-197">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="02732-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="02732-198">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="02732-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="02732-199">구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="02732-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="02732-200">기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속됩니다. 여기서 `T`는 기능의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="02732-201">기능 공급자는 이전에 나열된 모든 기능 형식에 대해 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="02732-202">`ApplicationPartManager.FeatureProviders`의 기능 공급자 순서는 런타임 동작에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="02732-203">나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="02732-204">사용 가능 기능 표시</span><span class="sxs-lookup"><span data-stu-id="02732-204">Display available features</span></span>

<span data-ttu-id="02732-205">앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="02732-206">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="02732-207">애플리케이션 파트에서 검색</span><span class="sxs-lookup"><span data-stu-id="02732-207">Discovery in application parts</span></span>

<span data-ttu-id="02732-208">HTTP 404 오류는 애플리케이션 파트를 사용하여 개발할 때 일반적이지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="02732-209">이러한 오류는 일반적으로 애플리케이션 파트를 검색하는 방법에 대한 필수 요구 사항이 없기 때문에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="02732-210">앱에서 HTTP 404 오류를 반환하는 경우 다음 요구 사항이 충족되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="02732-211">`applicationName` 설정은 검색에 사용되는 루트 어셈블리로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="02732-212">검색에 사용되는 루트 어셈블리는 일반적으로 진입점 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="02732-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="02732-213">루트 어셈블리에는 검색에 사용되는 파트에 대한 참조가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="02732-214">참조는 직접적 또는 전이적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="02732-215">루트 어셈블리는 웹 SDK를 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02732-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="02732-216">ASP.NET Core 프레임워크에는 검색에 사용되는 루트 어셈블리에 특성을 스탬핑하는 사용자 지정 빌드 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02732-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
