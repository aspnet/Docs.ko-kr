---
title: 클래스 라이브러리에서 ASP.NET Core API 사용
author: scottaddie
description: 클래스 라이브러리에서 ASP.NET Core API를 사용하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 571e6c66f60bbc09b902ff9064d2fb1c18c433dc
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "88630057"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="2e37f-103">클래스 라이브러리에서 ASP.NET Core API 사용</span><span class="sxs-lookup"><span data-stu-id="2e37f-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="2e37f-104">작성자: [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2e37f-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2e37f-105">이 문서에서는 클래스 라이브러리에서 ASP.NET Core API를 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="2e37f-106">다른 모든 라이브러리 지침은 [오픈 소스 라이브러리 지침](/dotnet/standard/library-guidance/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="2e37f-107">지원할 ASP.NET Core 버전 결정</span><span class="sxs-lookup"><span data-stu-id="2e37f-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="2e37f-108">ASP.NET Core는 [.NET Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)을 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="2e37f-109">라이브러리에서 지원할 ASP.NET Core 버전을 결정할 때 지원 정책을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="2e37f-110">라이브러리는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-110">A library should:</span></span>

* <span data-ttu-id="2e37f-111">*LTS* (장기 지원)로 분류된 모든 ASP.NET Core 버전을 지원하도록 노력합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="2e37f-112">*EOL* (수명 종료)로 분류된 ASP.NET Core 버전을 지원할 의무는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="2e37f-113">ASP.NET Core 미리 보기 릴리스가 출시되면 [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub 리포지토리에 최신 변경 내용이 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="2e37f-114">프레임워크 기능이 개발되는 동안 라이브러리의 호환성 테스트를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="2e37f-115">.NET Core 공유 프레임워크 사용</span><span class="sxs-lookup"><span data-stu-id="2e37f-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="2e37f-116">.NET Core 3.0이 출시되면 많은 ASP.NET Core 어셈블리가 더 이상 NuGet에 패키지로 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="2e37f-117">대신 어셈블리는 .NET Core SDK 및 런타임 설치 관리자와 함께 설치되는 `Microsoft.AspNetCore.App` 공유 프레임워크에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="2e37f-118">더 이상 게시되지 않는 패키지 목록은 [사용되지 않는 패키지 참조 제거](xref:migration/22-to-30#remove-obsolete-package-references)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="2e37f-119">.NET Core 3.0부터 `Microsoft.NET.Sdk.Web` MSBuild SDK를 사용하는 프로젝트는 공유 프레임워크를 암시적으로 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="2e37f-120">`Microsoft.NET.Sdk` 또는 `Microsoft.NET.Sdk.:::no-loc(Razor):::` SDK를 사용하는 프로젝트는 공유 프레임워크에서 ASP.NET Core API를 사용하도록 ASP.NET Core을 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.:::no-loc(Razor):::` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="2e37f-121">ASP.NET Core를 참조하려면 프로젝트 파일에 다음 `<FrameworkReference>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="2e37f-122">이러한 방식의 ASP.NET Core 참조는 .NET Core 3.x를 대상으로 하는 프로젝트에만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-no-locblazor-extensibility"></a><span data-ttu-id="2e37f-123">:::no-loc(Blazor)::: 확장성 포함</span><span class="sxs-lookup"><span data-stu-id="2e37f-123">Include :::no-loc(Blazor)::: extensibility</span></span>

<span data-ttu-id="2e37f-124">:::no-loc(Blazor):::는 WASM(WebAssembly) 및 Server [호스팅 모델](xref:blazor/hosting-models)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-124">:::no-loc(Blazor)::: supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="2e37f-125">특별한 이유가 없는 한 [:::no-loc(Razor)::: 구성 요소](xref:blazor/components/index) 라이브러리에서 두 호스팅 모델을 모두 지원해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-125">Unless there's a specific reason not to, a [:::no-loc(Razor)::: components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="2e37f-126">:::no-loc(Razor)::: 구성 요소 라이브러리는 [Microsoft.NET.Sdk.:::no-loc(Razor)::: SDK](xref:razor-pages/sdk)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-126">A :::no-loc(Razor)::: components library must use the [Microsoft.NET.Sdk.:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="2e37f-127">두 호스팅 모델 모두 지원</span><span class="sxs-lookup"><span data-stu-id="2e37f-127">Support both hosting models</span></span>

<span data-ttu-id="2e37f-128">[:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) 및 [:::no-loc(Blazor)::: WASM](xref:blazor/hosting-models#blazor-webassembly) 프로젝트 모두에서 :::no-loc(Razor)::: 구성 요소를 사용하도록 지원하려면 편집기에 대해 다음 지침을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-128">To support :::no-loc(Razor)::: component consumption from both [:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) and [:::no-loc(Blazor)::: WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e37f-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e37f-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2e37f-130">**:::no-loc(Razor)::: 클래스 라이브러리** 프로젝트 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-130">Use the **:::no-loc(Razor)::: Class Library** project template.</span></span> <span data-ttu-id="2e37f-131">템플릿의 **지원 페이지 및 보기** 확인란을 선택 취소해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e37f-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e37f-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e37f-133">통합 터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e37f-134">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e37f-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2e37f-135">**:::no-loc(Razor)::: 클래스 라이브러리** 프로젝트 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-135">Use the **:::no-loc(Razor)::: Class Library** project template.</span></span>

---

<span data-ttu-id="2e37f-136">템플릿에서 생성된 프로젝트는 다음과 같은 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="2e37f-137">.NET Standard 2.0을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="2e37f-138">`:::no-loc(Razor):::LangVersion` 속성을 `3.0`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-138">Sets the `:::no-loc(Razor):::LangVersion` property to `3.0`.</span></span> <span data-ttu-id="2e37f-139">`3.0`은 .NET Core 3.x의 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="2e37f-140">다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="2e37f-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="2e37f-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="2e37f-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="2e37f-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="2e37f-143">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="2e37f-144">특정 호스팅 모델 지원</span><span class="sxs-lookup"><span data-stu-id="2e37f-144">Support a specific hosting model</span></span>

<span data-ttu-id="2e37f-145">단일 :::no-loc(Blazor)::: 호스팅 모델을 지원하는 것은 일반적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-145">It's far less common to support a single :::no-loc(Blazor)::: hosting model.</span></span> <span data-ttu-id="2e37f-146">예를 들어 [:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) 프로젝트에서만 :::no-loc(Razor)::: 구성 요소를 사용하도록 지원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-146">As an example, to support :::no-loc(Razor)::: component consumption from [:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="2e37f-147">.NET Core 3.x를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="2e37f-148">공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="2e37f-149">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="2e37f-150">:::no-loc(Razor)::: 구성 요소가 포함된 라이브러리에 대한 자세한 내용은 [ASP.NET Core :::no-loc(Razor)::: 구성 요소 클래스 라이브러리](xref:blazor/components/class-libraries)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-150">For more information on libraries containing :::no-loc(Razor)::: components, see [ASP.NET Core :::no-loc(Razor)::: components class libraries](xref:blazor/components/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="2e37f-151">MVC 확장성 포함</span><span class="sxs-lookup"><span data-stu-id="2e37f-151">Include MVC extensibility</span></span>

<span data-ttu-id="2e37f-152">이 섹션에서는 다음을 포함하는 라이브러리에 대한 권장 사항을 개략적으로 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="2e37f-153">:::no-loc(Razor)::: 보기 또는 :::no-loc(Razor)::: Pages</span><span class="sxs-lookup"><span data-stu-id="2e37f-153">:::no-loc(Razor)::: views or :::no-loc(Razor)::: Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="2e37f-154">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="2e37f-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="2e37f-155">뷰 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2e37f-155">View components</span></span>](#view-components)

<span data-ttu-id="2e37f-156">이 섹션에서는 여러 버전의 MVC를 지원하는 멀티 타기팅에 대해서는 설명하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="2e37f-157">여러 ASP.NET Core 버전을 지원하는 방법에 대한 지침은 [여러 ASP.NET Core 버전 지원](#support-multiple-aspnet-core-versions)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="no-locrazor-views-or-no-locrazor-pages"></a><span data-ttu-id="2e37f-158">:::no-loc(Razor)::: 보기 또는 :::no-loc(Razor)::: Pages</span><span class="sxs-lookup"><span data-stu-id="2e37f-158">:::no-loc(Razor)::: views or :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="2e37f-159">[:::no-loc(Razor)::: 보기](xref:mvc/views/overview) 또는 [:::no-loc(Razor)::: Pages](xref:razor-pages/index)를 포함하는 프로젝트는 [Microsoft.NET.Sdk.:::no-loc(Razor)::: SDK](xref:razor-pages/sdk)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-159">A project that includes [:::no-loc(Razor)::: views](xref:mvc/views/overview) or [:::no-loc(Razor)::: Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="2e37f-160">프로젝트가 .NET Core 3.x를 대상으로 하는 경우 다음이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="2e37f-161">`true`로 설정된 `Add:::no-loc(Razor):::SupportForMvc` MSBuild 속성.</span><span class="sxs-lookup"><span data-stu-id="2e37f-161">An `Add:::no-loc(Razor):::SupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="2e37f-162">공유 프레임워크에 대한 `<FrameworkReference>` 요소.</span><span class="sxs-lookup"><span data-stu-id="2e37f-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="2e37f-163">**:::no-loc(Razor)::: 클래스 라이브러리** 프로젝트 템플릿은 .NET Core 3.x를 대상으로 하는 프로젝트에 대해 위 요구 사항을 충족합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-163">The **:::no-loc(Razor)::: Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="2e37f-164">편집기에 대해 다음 지침을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2e37f-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e37f-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2e37f-166">**:::no-loc(Razor)::: 클래스 라이브러리** 프로젝트 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-166">Use the **:::no-loc(Razor)::: Class Library** project template.</span></span> <span data-ttu-id="2e37f-167">템플릿의 **지원 페이지 및 보기** 확인란을 선택해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2e37f-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2e37f-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2e37f-169">통합 터미널에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2e37f-170">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2e37f-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2e37f-171">지금은 프로젝트 템플릿이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-171">No project template support at this time.</span></span>

---

<span data-ttu-id="2e37f-172">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="2e37f-173">프로젝트가 대신 .NET Standard를 대상으로 하는 경우 [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="2e37f-174">`Microsoft.AspNetCore.Mvc` 패키지가 ASP.NET Core 3.0의 공유 프레임워크로 이동했으며, 따라서 더 이상 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="2e37f-175">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="2e37f-176">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="2e37f-176">Tag Helpers</span></span>

<span data-ttu-id="2e37f-177">[태그 도우미](xref:mvc/views/tag-helpers/intro)를 포함하는 프로젝트는 `Microsoft.NET.Sdk` SDK를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="2e37f-178">.NET Core 3.x를 대상으로 하는 경우 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="2e37f-179">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="2e37f-180">.NET Standard를 대상으로 하는 경우(ASP.NET Core 3.x보다 이전 버전을 지원하기 위해) [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::).</span></span> <span data-ttu-id="2e37f-181">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` 패키지는 공유 프레임워크로 이동했으며, 따라서 더 이상 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-181">The `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="2e37f-182">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="2e37f-183">보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2e37f-183">View components</span></span>

<span data-ttu-id="2e37f-184">[구성 요소 보기](xref:mvc/views/view-components)를 포함하는 프로젝트는 `Microsoft.NET.Sdk` SDK를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="2e37f-185">.NET Core 3.x를 대상으로 하는 경우 공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="2e37f-186">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="2e37f-187">.NET Standard를 대상으로 하는 경우(ASP.NET Core 3.x보다 이전 버전을 지원하기 위해) [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures)에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="2e37f-188">`Microsoft.AspNetCore.Mvc.ViewFeatures` 패키지는 공유 프레임워크로 이동했으며, 따라서 더 이상 게시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="2e37f-189">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="2e37f-190">여러 ASP.NET Core 버전 지원</span><span class="sxs-lookup"><span data-stu-id="2e37f-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="2e37f-191">멀티 타기팅은 ASP.NET Core의 여러 변형을 지원하는 라이브러리를 작성하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="2e37f-192">태그 도우미 라이브러리가 다음 ASP.NET Core 변형을 지원해야 하는 시나리오를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="2e37f-193">.NET Framework 4.6.1을 대상으로 하는 ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="2e37f-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="2e37f-194">.NET Core 2.x를 대상으로 하는 ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="2e37f-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="2e37f-195">.NET Core 3.x를 대상으로 하는 ASP.NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="2e37f-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="2e37f-196">다음 프로젝트 파일은 `TargetFrameworks` 속성을 통해 이러한 변형을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="2e37f-197">이전 프로젝트 파일 사용:</span><span class="sxs-lookup"><span data-stu-id="2e37f-197">With the preceding project file:</span></span>

* <span data-ttu-id="2e37f-198">모든 소비자에게 `Markdig` 패키지가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="2e37f-199">[Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::)에 대한 참조는</span><span class="sxs-lookup"><span data-stu-id="2e37f-199">A reference to [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::)</span></span> <span data-ttu-id="2e37f-200">.NET Framework 4.6.1 이상 또는 .NET Core 2.x를 대상으로 하는 소비자에게 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="2e37f-201">이전 버전과의 호환성으로 인해 패키지의 버전 2.1.0은 ASP.NET Core 2.2에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="2e37f-202">공유 프레임워크는 .NET Core 3.x를 대상으로 하는 소비자에 대해 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="2e37f-203">`Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` 패키지는 공유 프레임워크에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-203">The `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` package is included in the shared framework.</span></span>

<span data-ttu-id="2e37f-204">또는 .NET Core 2.1 및 .NET Framework 4.6.1을 모두 대상으로 지정하는 대신 .NET Standard 2.0을 대상으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="2e37f-205">이전 프로젝트 파일에는 다음과 같은 주의 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="2e37f-206">라이브러리에는 태그 도우미만 포함되어 있으므로 ASP.NET Core가 실행되는 특정 플랫폼, 즉 .NET Core 및 .NET Framework을 대상으로 지정하는 것이 더 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="2e37f-207">태그 도우미를 Unity, UWP, Xamarin 같은 다른 .NET Standard 2.0 호환 대상 프레임워크에서 사용할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="2e37f-208">.NET Framework의 .NET Standard 2.0을 사용하는 경우 .NET Framework 4.7.2에서 해결된 몇 가지 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="2e37f-209">.NET Framework 4.6.1을 대상으로 하여 .NET Framework 4.6.1 ~ 4.7.1을 사용하는 소비자에 대한 환경을 개선할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="2e37f-210">라이브러리가 플랫폼별 API를 호출해야 하는 경우 .NET Standard 대신 특정 .NET 구현을 대상으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="2e37f-211">자세한 내용은 [멀티 타기팅](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="2e37f-212">변경되지 않은 API 사용</span><span class="sxs-lookup"><span data-stu-id="2e37f-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="2e37f-213">미들웨어 라이브러리를 .NET Core 2.2에서 3.0으로 업그레이드하는 시나리오를 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="2e37f-214">라이브러리에서 사용되는 ASP.NET Core 미들웨어 API는 ASP.NET Core 2.2와 3.0 사이에서 변경되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="2e37f-215">.NET Core 3.0에서 미들웨어 라이브러리를 계속 지원하려면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="2e37f-216">[표준 라이브러리 지침](/dotnet/standard/library-guidance/)을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="2e37f-217">해당 어셈블리가 공유 프레임워크에 존재하지 않는 경우 각 API의 NuGet 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="2e37f-218">변경된 API 사용</span><span class="sxs-lookup"><span data-stu-id="2e37f-218">Use an API that changed</span></span>

<span data-ttu-id="2e37f-219">라이브러리를 .NET Core 2.2에서 .NET Core 3.0으로 업그레이드하는 시나리오를 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="2e37f-220">라이브러리에서 사용되는 ASP.NET Core API는 ASP.NET Core 3.0에 [주요 변경 내용](/dotnet/core/compatibility/breaking-changes)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2e37f-221">모든 버전에서 변경된 API를 사용하지 않도록 라이브러리를 다시 작성할 수 있는지 여부를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="2e37f-222">라이브러리를 다시 작성할 수 있는 경우 다시 작성하여 패키지 참조가 포함된 이전 대상 프레임워크(예: .NET Standard 2.0 또는 .NET Framework 4.6.1)를 계속 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="2e37f-223">라이브러리를 다시 작성할 수 없는 경우 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="2e37f-224">.NET Core 3.0에 대한 대상을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="2e37f-225">공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="2e37f-226">적절한 대상 프레임워크 기호와 함께 [#if 전처리기 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)을 사용하여 조건부로 코드를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="2e37f-227">예를 들어 HTTP 요청 및 응답 스트림에 대한 동기 읽기 및 쓰기는 ASP.NET Core 3.0부터 기본적으로 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="2e37f-228">ASP.NET Core 2.2는 기본적으로 동기식 동작을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="2e37f-229">I/O가 발생하는 경우 동기 읽기 및 쓰기를 사용하도록 설정해야 하는 미들웨어 라이브러리를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="2e37f-230">라이브러리는 적절한 전처리기 지시문에서 동기 기능을 사용하도록 설정하는 코드를 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="2e37f-231">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="2e37f-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="2e37f-232">3\.0에 도입된 API 사용</span><span class="sxs-lookup"><span data-stu-id="2e37f-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="2e37f-233">ASP.NET Core 3.0에 도입된 ASP.NET Core API를 사용하려 한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2e37f-234">다음 질문을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-234">Consider the following questions:</span></span>

1. <span data-ttu-id="2e37f-235">라이브러리가 기능하려면 새 API가 필요한가요?</span><span class="sxs-lookup"><span data-stu-id="2e37f-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="2e37f-236">라이브러리에서 이 기능을 다른 방식으로 구현할 수 있나요?</span><span class="sxs-lookup"><span data-stu-id="2e37f-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="2e37f-237">라이브러리에 API가 필요하고 이 API를 하위 수준에서 구현할 방법이 없는 경우:</span><span class="sxs-lookup"><span data-stu-id="2e37f-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="2e37f-238">.NET Core 3.x만을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="2e37f-239">공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="2e37f-240">라이브러리가 다른 방식으로 기능을 구현할 수 있는 경우:</span><span class="sxs-lookup"><span data-stu-id="2e37f-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="2e37f-241">.NET Core 3.x를 대상 프레임워크로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="2e37f-242">공유 프레임워크에 대한 `<FrameworkReference>` 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="2e37f-243">적절한 대상 프레임워크 기호와 함께 [#if 전처리기 지시문](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)을 사용하여 조건부로 코드를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="2e37f-244">예를 들어 다음 태그 도우미는 ASP.NET Core 3.0에 도입된 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 인터페이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2e37f-245">.NET Core 3.0을 대상으로 하는 소비자는 `NETCOREAPP3_0` 대상 프레임워크 기호로 정의된 코드 경로를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="2e37f-246">태그 도우미의 생성자 매개 변수 형식이 .NET Core 2.1 및 .NET Framework 4.6.1 소비자에 대한 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="2e37f-247">이 변경은 ASP.NET Core 3.0이 `IHostingEnvironment`를 사용되지 않음으로 표시하고 `IWebHostEnvironment`를 대체 형식으로 권장했기 때문에 필요했습니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="2e37f-248">다음의 멀티 타게팅 프로젝트 파일은 이 태그 도우미 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="2e37f-249">공유 프레임워크에서 제거된 API 사용</span><span class="sxs-lookup"><span data-stu-id="2e37f-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="2e37f-250">공유 프레임워크에서 제거된 ASP.NET Core 어셈블리를 사용하려면 적절한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="2e37f-251">ASP.NET Core 3.0의 공유 프레임워크에서 제거되는 패키지 목록은 [사용되지 않는 패키지 참조 제거](xref:migration/22-to-30#remove-obsolete-package-references)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2e37f-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="2e37f-252">예를 들어 웹 API 클라이언트를 추가하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2e37f-252">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="2e37f-253">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2e37f-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="2e37f-254">.NET 구현 지원</span><span class="sxs-lookup"><span data-stu-id="2e37f-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="2e37f-255">.NET 지원 정책</span><span class="sxs-lookup"><span data-stu-id="2e37f-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
