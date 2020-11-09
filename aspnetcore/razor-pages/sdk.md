---
title: 'ASP.NET Core Razor SDK'
author: Rick-Anderson
description: '페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor Pages를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 알아봅니다.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060874"
---
# <a name="aspnet-core-no-locrazor-sdk"></a><span data-ttu-id="d3012-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="d3012-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="d3012-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d3012-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="d3012-105">개요</span><span class="sxs-lookup"><span data-stu-id="d3012-105">Overview</span></span>

<span data-ttu-id="d3012-106">[!INCLUDE[](~/includes/2.1-SDK.md)]에는 `Microsoft.NET.Sdk.Razor` MSBuild SDK(Razor SDK)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="d3012-107">Razor SDK:</span><span class="sxs-lookup"><span data-stu-id="d3012-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="d3012-108">ASP.NET Core MVC 기반 또는 [Blazor](xref:blazor/index) 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트를 빌드, 패키지 및 게시하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="d3012-109">Razor( *.cshtml* 또는 *.razor* ) 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 세트가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor ( *.cshtml* or *.razor* ) files.</span></span>

<span data-ttu-id="d3012-110">Razor SDK에는 `Include` 특성이 `**\*.cshtml` 및 `**\*.razor` 와일드카드 사용 패턴으로 설정된 `Content` 항목이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="d3012-111">일치 파일이 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="d3012-112">ASP.NET Core MVC 기반 프로젝트용 [Razor](xref:mvc/views/razor) 파일을 포함하는 프로젝트의 빌드, 패키징 및 게시와 관련된 환경을 표준화합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="d3012-113">Razor 파일 컴파일의 사용자 지정을 허용하는 사전 정의된 대상, 속성 및 항목 집합이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="d3012-114">Razor SDK에는 `Include` 특성이 `**\*.cshtml` 와일드카드 사용 패턴으로 설정된 `Content` 항목이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="d3012-115">일치 파일이 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="d3012-116">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d3012-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a><span data-ttu-id="d3012-117">Razor SDK 사용</span><span class="sxs-lookup"><span data-stu-id="d3012-117">Use the Razor SDK</span></span>

<span data-ttu-id="d3012-118">대부분의 웹앱은 Razor SDK를 명시적으로 참조할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3012-119">Razor SDK를 사용하여 Razor 뷰나 Razor Pages를 포함하는 클래스 라이브러리를 빌드하려면 RCL(Razor 클래스 라이브러리) 프로젝트 템플릿을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="d3012-120">Blazor( *.razor* ) 파일을 빌드하는 데 사용되는 RCL에는 [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) 패키지에 대한 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-120">An RCL that's used to build Blazor ( *.razor* ) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="d3012-121">Razor 뷰 또는 페이지( *.cshtml* 파일)를 빌드하는 데 사용되는 RCL은 적어도 `netcoreapp3.0` 이상을 대상으로 지정해야 하며 해당 프로젝트 파일에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 대한 `FrameworkReference`가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-121">An RCL that's used to build Razor views or pages ( *.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3012-122">Razor SDK를 사용하여 Razor 뷰 또는 Razor Pages를 포함하는 클래스 라이브러리를 빌드하려면 다음을 수행 및 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="d3012-123">`Microsoft.NET.Sdk` 대신 `Microsoft.NET.Sdk.Razor`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="d3012-124">일반적으로 Razor Pages 및 Razor 뷰를 빌드하고 컴파일하는 데 필요한 추가 종속성을 수신하려면 `Microsoft.AspNetCore.Mvc`에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="d3012-125">최소한 프로젝트는 다음에 패키지 참조를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="d3012-126">`Microsoft.AspNetCore.Razor.Design` 패키지는 프로젝트에 대한 Razor 컴파일 작업 및 대상을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="d3012-127">이전 패키지는 `Microsoft.AspNetCore.Mvc`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="d3012-128">다음 태그에서는 Razor SDK를 사용하여 ASP.NET Core Razor Pages 앱용 Razor 파일을 빌드하는 프로젝트 파일을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="d3012-129">`Microsoft.AspNetCore.Razor.Design` 및 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d3012-130">그러나 더 낮은 버전의 `Microsoft.AspNetCore.App` 패키지 참조는 최신 `Microsoft.AspNetCore.Razor.Design` 버전을 포함하지 않는 앱에 대한 메타패키지를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="d3012-131">프로젝트는 Razor에 대한 최신 빌드 타임 수정 사항이 포함되도록 일관된 `Microsoft.AspNetCore.Razor.Design` 버전(또는 `Microsoft.AspNetCore.Mvc`)을 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="d3012-132">자세한 내용은 [이 GitHub 이슈](https://github.com/aspnet/Razor/issues/2553)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3012-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="d3012-133">속성</span><span class="sxs-lookup"><span data-stu-id="d3012-133">Properties</span></span>

<span data-ttu-id="d3012-134">다음 속성은 Razor의 SDK 동작을 프로젝트 빌드의 일부로 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="d3012-135">`RazorCompileOnBuild`: `true`인 경우, 프로젝트 빌드 중에 Razor 어셈블리를 컴파일하고 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-135">`RazorCompileOnBuild`: When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="d3012-136">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-136">Defaults to `true`.</span></span>
* <span data-ttu-id="d3012-137">`RazorCompileOnPublish`: `true`인 경우, 프로젝트 게시 중에 Razor 어셈블리를 컴파일하고 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-137">`RazorCompileOnPublish`: When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="d3012-138">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-138">Defaults to `true`.</span></span>

<span data-ttu-id="d3012-139">다음 표의 속성 및 항목은 Razor SDK에 대한 입력 및 출력을 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="d3012-140">ASP.NET Core 3.0부터 프로젝트 파일의 `RazorCompileOnBuild` 또는 `RazorCompileOnPublish` MSBuild 속성을 사용할 수 없는 경우 MVC 뷰 또는 Razor Pages가 기본적으로 제공되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="d3012-141">애플리케이션은 런타임 컴파일을 사용하여 *.cshtml* 파일을 처리하는 경우 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) 패키지에 대한 명시적 참조를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="d3012-142">항목</span><span class="sxs-lookup"><span data-stu-id="d3012-142">Items</span></span> | <span data-ttu-id="d3012-143">설명</span><span class="sxs-lookup"><span data-stu-id="d3012-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="d3012-144">코드 생성에 입력되는 항목 요소( *.cshtml* 파일)입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-144">Item elements ( *.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="d3012-145">Razor 구성 요소 코드 생성에 입력되는 항목 요소( *.razor* 파일)입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-145">Item elements ( *.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="d3012-146">Razor 컴파일 대상에 입력되는 항목 요소( *.cs* 파일)입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-146">Item elements ( *.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="d3012-147">이 `ItemGroup`을 사용하여 Razor 어셈블리에 컴파일할 추가 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="d3012-148">코드에 사용된 항목 요소는 Razor 어셈블리의 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="d3012-149">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="d3012-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="d3012-150">생성된 Razor 어셈블리에 포함 리소스로 추가된 항목 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="d3012-151">속성</span><span class="sxs-lookup"><span data-stu-id="d3012-151">Property</span></span> | <span data-ttu-id="d3012-152">설명</span><span class="sxs-lookup"><span data-stu-id="d3012-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="d3012-153">Razor에서 생성한 어셈블리의 파일 이름(확장명 제외)입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="d3012-154">Razor 출력 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="d3012-155">Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="d3012-156">유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="d3012-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="d3012-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="d3012-158">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-158">Default is `true`.</span></span> <span data-ttu-id="d3012-159">`true`인 경우 *web.config* , *.json* 및 *.cshtml* 파일을 프로젝트의 콘텐츠로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-159">When `true`, includes *web.config* , *.json* , and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="d3012-160">`Microsoft.NET.Sdk.Web`를 통해 참조되는 경우 *wwwroot* 의 파일 및 구성 파일도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="d3012-161">`true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="d3012-162">`true`인 경우, `RazorAssemblyAttribute`로 지정된 특성을 포함하는 *.cs* 파일을 생성하고 이 파일을 컴파일 출력에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="d3012-163">`true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="d3012-164">`true`인 경우, `RazorGenerate` 항목( *.cshtml* ) 파일을 게시 디렉터리에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-164">When `true`, copies `RazorGenerate` items ( *.cshtml* ) files to the publish directory.</span></span> <span data-ttu-id="d3012-165">일반적으로 Razor 파일은 빌드 시간 또는 게시 시간에 컴파일에 참여하는 경우 게시된 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="d3012-166">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-166">Defaults to `false`.</span></span> |
| `PreserveCompilationReferences` | <span data-ttu-id="d3012-167">`true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="d3012-168">일반적으로 참조 어셈블리는 Razor 컴파일이 빌드 시간 또는 게시 시간에 발생하는 경우 게시된 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="d3012-169">게시된 앱에 런타임 컴파일이 필요한 경우 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="d3012-170">예를 들어, 앱이 런타임에 *.cshtml* 파일을 수정하거나 포함된 뷰를 사용하는 경우 값을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="d3012-171">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="d3012-172">`true`인 경우, 모든 Razor 콘텐츠 항목( *.cshtml* 파일)은 생성된 NuGet 패키지에 포함되도록 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-172">When `true`, all Razor content items ( *.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="d3012-173">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="d3012-174">`true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate( *.cshtml* ) 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-174">When `true`, adds RazorGenerate ( *.cshtml* ) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="d3012-175">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="d3012-176">`true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="d3012-177">기본값은 `UseSharedCompilation`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="d3012-178">`true`인 경우 SDK는 런타임에 MVC에서 애플리케이션 파트 검색을 수행하는 데 사용되는 추가 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="d3012-179">웹 또는 Razor SDK를 대상으로 하는 프로젝트의 `Content` 항목 그룹에서 제외될 항목 요소에 대한 globbing 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="d3012-180">`true`인 경우 *.config* 및 *.json* 파일은 빌드 출력 디렉터리에 복사되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="d3012-181">`true`인 경우 MVC 뷰나 Razor Pages를 포함하는 애플리케이션을 빌드할 때 필요한 MVC 구성에 대한 지원을 추가하도록 Razor SDK를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="d3012-182">이 속성은 웹 SDK를 대상으로 하는 .NET Core 3.0 이상 프로젝트용으로 암시적으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="d3012-183">대상으로 지정할 Razor 언어의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-183">The version of the Razor Language to target.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="d3012-184">속성</span><span class="sxs-lookup"><span data-stu-id="d3012-184">Property</span></span> | <span data-ttu-id="d3012-185">설명</span><span class="sxs-lookup"><span data-stu-id="d3012-185">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="d3012-186">Razor에서 생성한 어셈블리의 파일 이름(확장명 제외)입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-186">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="d3012-187">Razor 출력 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-187">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="d3012-188">Razor 어셈블리를 빌드하는 데 사용되는 도구 집합을 결정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-188">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="d3012-189">유효한 값은 `Implicit`, `RazorSDK` 및 `PrecompilationTool`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-189">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="d3012-190">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="d3012-190">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="d3012-191">기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-191">Default is `true`.</span></span> <span data-ttu-id="d3012-192">`true`인 경우 *web.config* , *.json* 및 *.cshtml* 파일을 프로젝트의 콘텐츠로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-192">When `true`, includes *web.config* , *.json* , and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="d3012-193">`Microsoft.NET.Sdk.Web`를 통해 참조되는 경우 *wwwroot* 의 파일 및 구성 파일도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-193">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="d3012-194">`true`인 경우, `RazorGenerate` 항목의 `Content` 항목에서 *.cshtml* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-194">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="d3012-195">`true`인 경우, `RazorAssemblyAttribute`로 지정된 특성을 포함하는 *.cs* 파일을 생성하고 이 파일을 컴파일 출력에 포함시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-195">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="d3012-196">`true`인 경우, `RazorAssemblyAttribute`에 어셈블리 특성의 기본 집합을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-196">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="d3012-197">`true`인 경우, `RazorGenerate` 항목( *.cshtml* ) 파일을 게시 디렉터리에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-197">When `true`, copies `RazorGenerate` items ( *.cshtml* ) files to the publish directory.</span></span> <span data-ttu-id="d3012-198">일반적으로 Razor 파일은 빌드 시간 또는 게시 시간에 컴파일에 참여하는 경우 게시된 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-198">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="d3012-199">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-199">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="d3012-200">`true`인 경우, 참조 어셈블리 항목을 게시 디렉터리에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-200">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="d3012-201">일반적으로 참조 어셈블리는 Razor 컴파일이 빌드 시간 또는 게시 시간에 발생하는 경우 게시된 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-201">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="d3012-202">게시된 앱에 런타임 컴파일이 필요한 경우 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-202">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="d3012-203">예를 들어, 앱이 런타임에 *.cshtml* 파일을 수정하거나 포함된 뷰를 사용하는 경우 값을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-203">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="d3012-204">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-204">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="d3012-205">`true`인 경우, 모든 Razor 콘텐츠 항목( *.cshtml* 파일)은 생성된 NuGet 패키지에 포함되도록 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-205">When `true`, all Razor content items ( *.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="d3012-206">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-206">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="d3012-207">`true`인 경우, 생성된 Razor 어셈블리에 포함된 파일로 RazorGenerate( *.cshtml* ) 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-207">When `true`, adds RazorGenerate ( *.cshtml* ) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="d3012-208">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-208">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="d3012-209">`true`인 경우, 영구적 빌드 서버 프로세스를 사용하여 코드 생성 작업을 오프로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-209">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="d3012-210">기본값은 `UseSharedCompilation`입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-210">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="d3012-211">`true`인 경우 SDK는 런타임에 MVC에서 애플리케이션 파트 검색을 수행하는 데 사용되는 추가 특성을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-211">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="d3012-212">웹 또는 Razor SDK를 대상으로 하는 프로젝트의 `Content` 항목 그룹에서 제외될 항목 요소에 대한 globbing 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-212">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="d3012-213">`true`인 경우 *.config* 및 *.json* 파일은 빌드 출력 디렉터리에 복사되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-213">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="d3012-214">`true`인 경우 MVC 뷰나 Razor Pages를 포함하는 애플리케이션을 빌드할 때 필요한 MVC 구성에 대한 지원을 추가하도록 Razor SDK를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-214">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="d3012-215">이 속성은 웹 SDK를 대상으로 하는 .NET Core 3.0 이상 프로젝트용으로 암시적으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-215">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="d3012-216">대상으로 지정할 Razor 언어의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-216">The version of the Razor Language to target.</span></span> |

::: moniker-end

<span data-ttu-id="d3012-217">속성에 대한 자세한 내용은 [MSBuild 속성](/visualstudio/msbuild/msbuild-properties)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d3012-217">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="d3012-218">대상</span><span class="sxs-lookup"><span data-stu-id="d3012-218">Targets</span></span>

<span data-ttu-id="d3012-219">Razor SDK는 두 기본 대상을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-219">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="d3012-220">`RazorGenerate`: 코드는 `RazorGenerate` 항목 요소에서 *.cs* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-220">`RazorGenerate`: Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="d3012-221">이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorGenerateDependsOn` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-221">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="d3012-222">`RazorCompile`: 생성된 *.cs* 파일을 Razor 어셈블리로 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-222">`RazorCompile`: Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="d3012-223">이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorCompileDependsOn`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-223">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="d3012-224">`RazorComponentGenerate`: 코드는 `RazorComponent` 항목 요소에 대한 *.cs* 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-224">`RazorComponentGenerate`: Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="d3012-225">이 대상 전후에 실행할 수 있는 추가 대상을 지정하려면 `RazorComponentGenerateDependsOn` 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-225">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-no-locrazor-views"></a><span data-ttu-id="d3012-226">Razor 뷰의 런타임 컴파일</span><span class="sxs-lookup"><span data-stu-id="d3012-226">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="d3012-227">기본적으로 Razor SDK는 런타임 컴파일을 수행하는 데 필요한 참조 어셈블리를 게시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-227">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="d3012-228">이로 인해 애플리케이션 모델이 런타임 컴파일을 사용하는 경우 컴파일 오류가 발생합니다&mdash;(예: 앱이 게시된 후에 앱에서는 포함된 보기 또는 변경 내용 보기를 사용함).</span><span class="sxs-lookup"><span data-stu-id="d3012-228">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="d3012-229">`CopyRefAssembliesToPublishDirectory`를 `true`로 설정하여 계속 참조 어셈블리를 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-229">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="d3012-230">웹앱의 경우 앱이 `Microsoft.NET.Sdk.Web` SDK를 대상으로 지정하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-230">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="no-locrazor-language-version"></a><span data-ttu-id="d3012-231">Razor 언어 버전</span><span class="sxs-lookup"><span data-stu-id="d3012-231">Razor language version</span></span>

<span data-ttu-id="d3012-232">`Microsoft.NET.Sdk.Web` SDK를 대상으로 지정하는 경우 앱의 대상 프레임워크 버전에서 Razor 언어 버전이 유추됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-232">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="d3012-233">`Microsoft.NET.Sdk.Razor` SDK를 대상으로 하는 프로젝트 또는 앱이 유추된 값과는 다른 Razor 언어 버전이 필요한 드문 경우에 앱의 프로젝트 파일에서 `<RazorLangVersion>` 속성을 설정하여 버전을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-233">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="d3012-234">Razor의 언어 버전은 빌드된 런타임 버전과 긴밀하게 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-234">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="d3012-235">런타임용으로 디자인된 언어 버전을 대상으로 하는 것은 지원되지 않으며 빌드 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d3012-235">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3012-236">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d3012-236">Additional resources</span></span>

* [<span data-ttu-id="d3012-237">.NET Core용 csproj 형식에 대한 추가 사항</span><span class="sxs-lookup"><span data-stu-id="d3012-237">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="d3012-238">일반적인 MSBuild 프로젝트 항목</span><span class="sxs-lookup"><span data-stu-id="d3012-238">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
