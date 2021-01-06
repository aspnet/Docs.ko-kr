---
title: ASP.NET Core를 사용하여 클래스 라이브러리에서 재사용 가능한 Razor UI
author: Rick-Anderson
description: ASP.NET Core에서 클래스 라이브러리의 부분 뷰를 사용하여 재사용 가능한 Razor UI를 만드는 방법을 설명합니다.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
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
uid: razor-pages/ui-class
ms.openlocfilehash: 0bfdb1932d829ec00c9de1bd38b7920cb1f40c51
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94570174"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="54665-103">ASP.NET Core에서 Razor 클래스 라이브러리 프로젝트를 사용하여 재사용 가능한 UI 만들기</span><span class="sxs-lookup"><span data-stu-id="54665-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="54665-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="54665-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="54665-105">RCL(Razor 클래스 라이브러리)에 Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [Razor 구성 요소](xref:blazor/components/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components) 및 데이터 모델을 기본적으로 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="54665-106">RCL은 패키지되고 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="54665-107">애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="54665-108">뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="54665-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54665-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="54665-110">Razor UI를 포함하는 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="54665-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54665-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54665-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="54665-112">Visual Studio에서 **새 프로젝트 만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="54665-113">**Razor 클래스 라이브러리** > **다음** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="54665-114">라이브러리 이름을 지정하고(예: “RazorClassLib”), **만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="54665-115">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="54665-116">뷰를 지원해야 하는 경우 **페이지 및 뷰 지원** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="54665-117">기본적으로 Razor Pages만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="54665-118">**만들기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-118">Select **Create**.</span></span>

<span data-ttu-id="54665-119">RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="54665-120">**페이지 및 뷰 지원** 옵션은 페이지와 뷰를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="54665-121">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="54665-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="54665-122">명령줄에서 `dotnet new razorclasslib`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="54665-123">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="54665-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="54665-124">RCL(Razor 클래스 라이브러리) 템플릿은 기본적으로 Razor 구성 요소 개발로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="54665-125">페이지 및 뷰 지원을 제공하려면 `--support-pages-and-views` 옵션을 전달합니다(`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="54665-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="54665-126">자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="54665-127">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="54665-128">RCL에 Razor 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="54665-129">ASP.NET Core 템플릿은 RCL 콘텐츠가 *Areas* 폴더에 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="54665-130">`~/Areas/Pages`가 아닌 `~/Pages`의 콘텐츠를 공개하는 RCL을 만들려면 [RCL 페이지 레이아웃](#rcl-pages-layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="54665-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="54665-131">RCL 콘텐츠 참조</span><span class="sxs-lookup"><span data-stu-id="54665-131">Reference RCL content</span></span>

<span data-ttu-id="54665-132">RCL은 다음에서 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="54665-133">NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="54665-133">NuGet package.</span></span> <span data-ttu-id="54665-134">[NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="54665-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="54665-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="54665-136">[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="54665-137">보기, 부분 보기 및 페이지 재정의</span><span class="sxs-lookup"><span data-stu-id="54665-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="54665-138">뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="54665-139">예를 들어 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* 을 WebApp1에 추가하면, WebApp1의 Page1이 RCL의 Page1보다 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="54665-140">샘플 다운로드에서 *WebApp1/Areas/MyFeature2* 를 *WebApp1/Areas/MyFeature* 로 이름을 바꾸어 우선적으로 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="54665-141">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 뷰를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* 에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="54665-142">새 위치를 나타내기 위해 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="54665-143">해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="54665-144">RCL 페이지 레이아웃</span><span class="sxs-lookup"><span data-stu-id="54665-144">RCL Pages layout</span></span>

<span data-ttu-id="54665-145">웹앱의 *Pages* 폴더에 있는 것처럼 RCL 콘텐츠를 참조하려면 다음 파일 구조로 이루어진 RCL 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="54665-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="54665-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="54665-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="54665-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="54665-148">*RazorUIClassLib/Pages/Shared* 에 *_Header.cshtml* 및 *_Footer.cshtml* 이라는 두 개의 부분 파일이 들어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="54665-149">*_Layout.cshtml* 파일에 `<partial>` 태그를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="54665-150">정적 자산을 사용하여 RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="54665-150">Create an RCL with static assets</span></span>

<span data-ttu-id="54665-151">RCL 또는 RCL의 사용 앱에서 참조할 수 있는 도우미 정적 자산이 RCL에 필요할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="54665-152">ASP.NET Core에서는 사용 앱이 사용할 수 있는 정적 자산을 포함하는 RCL을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="54665-153">도우미 자산을 RCL의 일부로 포함하려면 클래스 라이브러리에 *wwwroot* 폴더를 만들고 필요한 모든 파일을 해당 폴더에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="54665-154">RCL을 패키지하면 *wwwroot* 폴더에 있는 모든 도우미 자산이 패키지에 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="54665-155">NuGet.exe 버전 `nuget pack` 대신 `dotnet pack` 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="54665-156">정적 자산 제외</span><span class="sxs-lookup"><span data-stu-id="54665-156">Exclude static assets</span></span>

<span data-ttu-id="54665-157">정적 자산을 제외하려면 프로젝트 파일의 `$(DefaultItemExcludes)` 속성 그룹에 원하는 제외 경로를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="54665-158">항목을 세미콜론(`;`)으로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="54665-159">다음 예제에서 *wwwroot* 폴더에 있는 *lib.css* 스타일시트는 정적 자산으로 간주되지 않아 게시된 RCL에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="54665-160">TypeScript 통합</span><span class="sxs-lookup"><span data-stu-id="54665-160">Typescript integration</span></span>

<span data-ttu-id="54665-161">RCL에 TypeScript 파일을 포함하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="54665-162">*wwwroot* 폴더 외부에 TypeScript 파일( *.ts*)을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-162">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="54665-163">예를 들어 *Client* 폴더에 파일을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="54665-164">*wwwroot* 폴더에 대한 TypeScript 빌드 출력을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="54665-165">프로젝트 파일의 `PropertyGroup` 내부에 `TypescriptOutDir` 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="54665-166">프로젝트 파일의 `PropertyGroup` 내부에 다음 대상을 추가하여 TypeScript 대상을 `ResolveCurrentProjectStaticWebAssets` 대상의 종속성으로 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="54665-167">참조된 RCL의 콘텐츠 사용</span><span class="sxs-lookup"><span data-stu-id="54665-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="54665-168">RCL의 *wwwroot* 폴더에 포함된 파일은 접두사 `_content/{LIBRARY NAME}/` 아래에서 RCL 또는 사용 앱에 공개됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="54665-169">예를 들어 *Razor.Class.Lib* 라는 라이브러리의 정적 콘텐츠 경로는 `_content/Razor.Class.Lib/`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="54665-170">NuGet 패키지를 생성할 때 어셈블리 이름이 패키지 ID와 다른 경우 `{LIBRARY NAME}`에 패키지 ID를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="54665-171">사용 앱은 `<script>`, `<style>`, `<img>` 및 기타 HTML 태그를 사용하여 라이브러리에서 제공하는 정적 자산을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="54665-172">`Startup.Configure`에서 사용 앱에 대해 [정적 파일 지원](xref:fundamentals/static-files)이 사용하도록 설정되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="54665-173">빌드 출력(`dotnet run`)에서 사용 앱을 실행하는 경우, 개발 환경에서는 정적 웹 자산이 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="54665-174">빌드 출력에서 실행할 때 다른 환경의 자산을 지원하려면 호스트 작성기의 *Program.cs* 에서 `UseStaticWebAssets`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="54665-175">게시된 출력(`dotnet publish`)에서 앱을 실행하는 경우 `UseStaticWebAssets`를 호출할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="54665-176">다중 프로젝트 개발 흐름</span><span class="sxs-lookup"><span data-stu-id="54665-176">Multi-project development flow</span></span>

<span data-ttu-id="54665-177">사용 앱이 실행되는 경우와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-177">When the consuming app runs:</span></span>

* <span data-ttu-id="54665-178">RCL의 자산은 원래 폴더에 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="54665-179">자산이 사용 앱으로 이동되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="54665-180">RCL *wwwroot* 폴더 내의 모든 변경 내용은 RCL을 다시 빌드한 후 사용 앱에 반영됩니다. 사용 앱을 다시 빌드할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="54665-181">RCL을 빌드하면 정적 웹 자산 위치를 설명하는 매니페스트가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="54665-182">사용 앱은 런타임에 매니페스트를 읽어 참조된 프로젝트와 패키지의 자산을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="54665-183">RCL에 새 자산이 추가된 경우 RCL을 다시 빌드해서 매니페스트를 업데이트해야 사용 앱이 새 자산에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="54665-184">게시</span><span class="sxs-lookup"><span data-stu-id="54665-184">Publish</span></span>

<span data-ttu-id="54665-185">앱을 게시하면 참조된 모든 프로젝트와 패키지의 도우미 자산이 `_content/{LIBRARY NAME}/` 아래에 있는 게시된 앱의 *wwwroot* 폴더에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54665-186">RCL(Razor 클래스 라이브러리)에 Razor 뷰, 페이지, 컨트롤러, 페이지 모델, [Razor 구성 요소](xref:blazor/components/class-libraries), [뷰 구성 요소](xref:mvc/views/view-components) 및 데이터 모델을 기본적으로 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-186">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="54665-187">RCL은 패키지되고 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="54665-188">애플리케이션은 RCL 포함할 수 있고 RCL이 포함하는 보기 및 페이지를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="54665-189">뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="54665-190">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54665-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="54665-191">Razor UI를 포함하는 클래스 라이브러리 만들기</span><span class="sxs-lookup"><span data-stu-id="54665-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54665-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54665-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="54665-193">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="54665-194">**새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="54665-195">라이브러리 이름 지정을 지정하고(예: “RazorClassLib”), **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="54665-196">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="54665-197">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="54665-198">**Razor 클래스 라이브러리** > **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="54665-199">RCL에는 다음과 같은 프로젝트 파일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="54665-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="54665-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="54665-201">명령줄에서 `dotnet new razorclasslib`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="54665-202">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="54665-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="54665-203">자세한 내용은 [dotnet new](/dotnet/core/tools/dotnet-new)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="54665-204">생성된 보기 라이브러리와 파일 이름 충돌을 방지하려면 라이브러리 이름이 `.Views`로 끝나지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="54665-205">RCL에 Razor 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="54665-206">ASP.NET Core 템플릿은 RCL 콘텐츠가 *Areas* 폴더에 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="54665-207">`~/Areas/Pages`가 아닌 `~/Pages`의 콘텐츠를 공개하는 RCL을 만들려면 [RCL 페이지 레이아웃](#rcl-pages-layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="54665-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="54665-208">RCL 콘텐츠 참조</span><span class="sxs-lookup"><span data-stu-id="54665-208">Reference RCL content</span></span>

<span data-ttu-id="54665-209">RCL은 다음에서 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="54665-210">NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="54665-210">NuGet package.</span></span> <span data-ttu-id="54665-211">[NuGet 패키지 만들기](/nuget/create-packages/creating-a-package), [dotnet 추가 패키지](/dotnet/core/tools/dotnet-add-package) 및 [NuGet 패키지 만들기 및 게시](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="54665-212">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="54665-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="54665-213">[dotnet-add reference](/dotnet/core/tools/dotnet-add-reference)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="54665-214">연습: RCL 프로젝트를 만들고 Razor Pages 프로젝트에서 사용</span><span class="sxs-lookup"><span data-stu-id="54665-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="54665-215">[전체 프로젝트](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)를 다운로드하여 만들지 않고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="54665-216">샘플 다운로드에는 프로젝트를 쉽게 테스트하게 하는 링크와 추가 코드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="54665-217">샘플 다운로드 대 단계별 지침에 대한 주석을 사용하여 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/6098)에서 사용자 의견을 그대로 둘 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="54665-218">다운로드 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="54665-218">Test the download app</span></span>

<span data-ttu-id="54665-219">완료된 앱을 다운로드하지 않고 연습 프로젝트를 만들려는 경우 [다음 섹션](#create-an-rcl)으로 건너 뜁니다.</span><span class="sxs-lookup"><span data-stu-id="54665-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54665-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54665-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="54665-221">Visual Studio에서 *.sln* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="54665-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="54665-222">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="54665-223">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="54665-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="54665-224">*cli* 디렉터리의 명령 프롬프트에서 RCL 및 웹앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="54665-225">*WebApp1* 디렉터리로 이동해 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="54665-226">[WebApp1 테스트](#test-webapp1)의 지침 준수</span><span class="sxs-lookup"><span data-stu-id="54665-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="54665-227">RCL 만들기</span><span class="sxs-lookup"><span data-stu-id="54665-227">Create an RCL</span></span>

<span data-ttu-id="54665-228">이 섹션에서는 RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-228">In this section, an RCL is created.</span></span> <span data-ttu-id="54665-229">Razor 파일이 RCL에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-229">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54665-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54665-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="54665-231">RCL 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-231">Create the RCL project:</span></span>

* <span data-ttu-id="54665-232">Visual Studio **파일** 메뉴에서 **새로 만들기** >**프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="54665-233">**새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="54665-234">앱 이름을 **RazorUIClassLib** 로 지정하고, **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="54665-235">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="54665-236">**Razor 클래스 라이브러리** > **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="54665-237">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 이라는 Razor 부분 뷰 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="54665-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="54665-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="54665-239">명령줄에서 다음을 실행하세요.</span><span class="sxs-lookup"><span data-stu-id="54665-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="54665-240">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="54665-240">The preceding commands:</span></span>

* <span data-ttu-id="54665-241">`RazorUIClassLib` RCL을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="54665-242">Razor _Message 페이지를 만들어 RCL에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="54665-243">`-np` 매개 변수는 `PageModel`가 없는 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="54665-244">[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) 파일을 만들어 RCL에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="54665-245">다음 섹션에서 추가하는 Razor Pages 프로젝트의 레이아웃을 사용하려면 *_ViewStart.cshtml* 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="54665-246">프로젝트에 Razor 파일 및 폴더 추가</span><span class="sxs-lookup"><span data-stu-id="54665-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="54665-247">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 에서 태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="54665-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="54665-248">*RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* 에서 태그를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="54665-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="54665-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`은 부분 보기(`<partial name="_Message" />`)를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="54665-250">`@addTagHelper` 지시문을 포함하지 않고 *_ViewImports.cshtml* 파일을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="54665-251">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="54665-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="54665-252">*_ViewImports.cshtml* 에 대한 자세한 내용은 [공유 지시문 가져오기](xref:mvc/views/layout#importing-shared-directives)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="54665-252">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="54665-253">컴파일러 오류가 없는지 확인하려면 클래스 라이브러리를 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="54665-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="54665-254">빌드 출력은 *RazorUIClassLib.dll* 및 *RazorUIClassLib.Views.dll* 을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="54665-255">*RazorUIClassLib.Views.dll* 은 컴파일된 Razor 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="54665-256">Razor Pages 프로젝트에서 Razor UI 라이브러리 사용</span><span class="sxs-lookup"><span data-stu-id="54665-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="54665-257">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54665-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="54665-258">Razor Pages 웹앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="54665-259">**솔루션 탐색기** 에서 솔루션을 마우스 오른쪽 단추로 클릭하고 > **추가** > **새 프로젝트** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-259">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="54665-260">**새 ASP.NET Core 웹 애플리케이션** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="54665-261">**WebApp1** 앱 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="54665-262">**ASP.NET Core 2.1** 이상이 선택됐는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="54665-263">**웹 애플리케이션** > **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="54665-264">**솔루션 탐색기** 에서 **WebApp1** 를 마우스 오른쪽 단추로 클릭하고 **스타트업 프로젝트로 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-264">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="54665-265">**솔루션 탐색기** 에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭하고 **빌드 종속성** > **프로젝트 종속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-265">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="54665-266">**RazorUIClassLib** 를 **WebApp1** 의 종속성으로 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="54665-267">**솔루션 탐색기** 에서 **WebApp1** 을 마우스 오른쪽 단추로 클릭하고 **추가** > **참조** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-267">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="54665-268">**참조 관리자** 대화 상자에서 **RazorUIClassLib** > **확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="54665-269">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="54665-270">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="54665-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="54665-271">Razor Pages 앱과 RCL을 포함하는 Razor Pages 웹앱 및 솔루션 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="54665-272">웹앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="54665-273">WebApp1 테스트</span><span class="sxs-lookup"><span data-stu-id="54665-273">Test WebApp1</span></span>

<span data-ttu-id="54665-274">`/MyFeature/Page1`로 이동하여 Razor UI 클래스 라이브러리가 사용 중인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="54665-275">보기, 부분 보기 및 페이지 재정의</span><span class="sxs-lookup"><span data-stu-id="54665-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="54665-276">뷰, 부분 뷰 또는 Razor 페이지가 웹앱 및 RCL 모두에 있는 경우 웹앱에서 Razor 태그( *.cshtml* 파일)가 우선으로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="54665-277">예를 들어 *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* 을 WebApp1에 추가하면, WebApp1의 Page1이 RCL의 Page1보다 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="54665-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="54665-278">샘플 다운로드에서 *WebApp1/Areas/MyFeature2* 를 *WebApp1/Areas/MyFeature* 로 이름을 바꾸어 우선적으로 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="54665-279">*RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* 부분 뷰를 *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml* 에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="54665-280">새 위치를 나타내기 위해 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="54665-281">해당 부분의 앱 버전이 사용되고 있는지 확인하려면 앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="54665-282">RCL 페이지 레이아웃</span><span class="sxs-lookup"><span data-stu-id="54665-282">RCL Pages layout</span></span>

<span data-ttu-id="54665-283">웹앱의 *Pages* 폴더에 있는 것처럼 RCL 콘텐츠를 참조하려면 다음 파일 구조로 이루어진 RCL 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="54665-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="54665-284">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="54665-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="54665-285">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="54665-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="54665-286">*RazorUIClassLib/Pages/Shared* 에 *_Header.cshtml* 및 *_Footer.cshtml* 이라는 두 개의 부분 파일이 들어 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="54665-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="54665-287">*_Layout.cshtml* 파일에 `<partial>` 태그를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="54665-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="54665-288">추가 자료</span><span class="sxs-lookup"><span data-stu-id="54665-288">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
