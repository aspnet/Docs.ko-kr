---
title: ASP.NET Core 앱 배포용 Visual Studio 게시 프로필(.pubxml)
author: rick-anderson
description: Visual Studio에서 게시 프로필을 만들고 다양한 대상에 대한 ASP.NET Core 앱 배포를 관리하는 데 사용하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: 98dfd539171807cbf94d4ac8746458152c809495
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057572"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="5a8a9-103">ASP.NET Core 앱 배포용 Visual Studio 게시 프로필(.pubxml)</span><span class="sxs-lookup"><span data-stu-id="5a8a9-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="5a8a9-104">작성자: [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a8a9-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5a8a9-105">이 문서에서는 Visual Studio 2019 이상을 사용하여 게시 프로필을 만들고 사용하는 방법에 초점을 맞춥니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="5a8a9-106">Visual Studio로 만들어진 게시 프로필은 MSBuild 및 Visual Studio와 함께 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="5a8a9-107">Azure에 게시하는 방법은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="5a8a9-108">`dotnet new mvc` 명령은 다음 루트 수준 [\<Project> 요소](/visualstudio/msbuild/project-element-msbuild)가 포함된 프로젝트 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="5a8a9-109">위 `<Project>` 요소의 `Sdk` 특성은 각각 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* 및 *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* 에서 MSBuild [속성](/visualstudio/msbuild/msbuild-properties) 및 [대상](/visualstudio/msbuild/msbuild-targets)을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* , respectively.</span></span> <span data-ttu-id="5a8a9-110">`$(MSBuildSDKsPath)`(Visual Studio 2019 Enterprise 사용)의 기본 위치는 *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="5a8a9-111">`Microsoft.NET.Sdk.Web`([웹 SDK](xref:razor-pages/web-sdk))은 `Microsoft.NET.Sdk`([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) 및 `Microsoft.NET.Sdk.Razor`([Razor SDK](xref:razor-pages/sdk))를 비롯한 다른 SDK에 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="5a8a9-112">각 종속 SDK와 연결된 MSBuild 속성과 대상을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="5a8a9-113">게시 대상은 사용된 게시 방법에 따라 해당 대상 집합을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="5a8a9-114">MSBuild 또는 Visual Studio가 프로젝트를 로드하면 다음 높은 수준의 작업이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="5a8a9-115">프로젝트 빌드</span><span class="sxs-lookup"><span data-stu-id="5a8a9-115">Build project</span></span>
* <span data-ttu-id="5a8a9-116">게시할 파일 컴퓨팅</span><span class="sxs-lookup"><span data-stu-id="5a8a9-116">Compute files to publish</span></span>
* <span data-ttu-id="5a8a9-117">대상에 파일 게시</span><span class="sxs-lookup"><span data-stu-id="5a8a9-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="5a8a9-118">프로젝트 항목 컴퓨팅</span><span class="sxs-lookup"><span data-stu-id="5a8a9-118">Compute project items</span></span>

<span data-ttu-id="5a8a9-119">프로젝트가 로드되면 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)(파일)이 컴퓨팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="5a8a9-120">항목 종류에 따라 파일 처리 방법이 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="5a8a9-121">기본적으로 *.cs* 파일은 `Compile` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="5a8a9-122">`Compile` 항목 목록의 파일이 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="5a8a9-123">`Content` 항목 목록에는 빌드 출력 이외에 게시될 파일이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="5a8a9-124">기본적으로 `wwwroot\**`, `**\*.json` 및 `Content` 패턴과 일치하는 파일이 `**\*.config` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="5a8a9-125">예를 들어 `wwwroot\**` [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)은 *wwwroot* 폴더 및 하위 폴더의 모든 파일과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a8a9-126">[웹 SDK](xref:razor-pages/web-sdk)는 [Razor SDK](xref:razor-pages/sdk)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="5a8a9-127">따라서 `**\*.cshtml` 및 `**\*.razor` 패턴과 일치하는 파일도 `Content` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="5a8a9-128">[웹 SDK](xref:razor-pages/web-sdk)는 [Razor SDK](xref:razor-pages/sdk)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="5a8a9-129">따라서 `**\*.cshtml` 패턴과 일치하는 파일도 `Content` 항목 목록에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="5a8a9-130">게시 목록에 파일을 명시적으로 추가하려면 [포함 파일](#include-files) 섹션에 표시된 대로 *.csproj* 파일에서 직접 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="5a8a9-131">Visual Studio에서 **게시** 단추를 선택하거나 명령줄에서 게시할 경우:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="5a8a9-132">속성/항목이 계산됩니다(빌드하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="5a8a9-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="5a8a9-133">**Visual Studio 전용** : NuGet 패키지가 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-133">**Visual Studio only** : NuGet packages are restored.</span></span> <span data-ttu-id="5a8a9-134">(CLI에서 사용자가 명시적으로 복원해야 합니다.)</span><span class="sxs-lookup"><span data-stu-id="5a8a9-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="5a8a9-135">프로젝트가 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-135">The project builds.</span></span>
* <span data-ttu-id="5a8a9-136">게시 항목이 계산됩니다(게시하는 데 필요한 파일).</span><span class="sxs-lookup"><span data-stu-id="5a8a9-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="5a8a9-137">프로젝트가 게시됩니다(계산된 파일이 게시 대상에 복사됨).</span><span class="sxs-lookup"><span data-stu-id="5a8a9-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="5a8a9-138">ASP.NET Core 프로젝트가 프로젝트 파일의 `Microsoft.NET.Sdk.Web`을 참조하면 *app_offline.htm* 파일은 웹앱 디렉터리의 루트에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="5a8a9-139">파일이 있는 경우 ASP.NET Core 모듈은 앱을 정상적으로 종료하고, 배포하는 동안 *app_offline.htm* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="5a8a9-140">자세한 내용은 [ASP.NET Core 모듈 구성 참조](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="5a8a9-141">기본 명령줄 게시</span><span class="sxs-lookup"><span data-stu-id="5a8a9-141">Basic command-line publishing</span></span>

<span data-ttu-id="5a8a9-142">명령줄 게시는 모든 .NET Core 지원 플랫폼에 적용되며 Visual Studio가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="5a8a9-143">다음 예제에서 .NET COre CLI의 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령은 *.csproj* 파일이 포함된 프로젝트 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="5a8a9-144">프로젝트 폴더가 현재 작업 디렉터리가 아닌 경우 프로젝트 파일 경로에서 명시적으로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="5a8a9-145">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="5a8a9-146">다음 명령을 실행하여 웹앱을 만들고 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="5a8a9-147">`dotnet publish` 명령은 다음 출력의 변형을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="5a8a9-148">기본 게시 폴더 형식은 *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="5a8a9-149">예를 들어 *bin\Debug\netcoreapp2.2\publish\\* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="5a8a9-150">다음 명령은 `Release` 빌드 및 게시 디렉터리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="5a8a9-151">`dotnet publish` 명령은 `Publish` 대상을 불러오는 MSBuild를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="5a8a9-152">`dotnet publish` 및 MSBuild에 전달된 모든 매개 변수.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="5a8a9-153">`-c` 및 `-o` 매개 변수는 각각 MSBuild의 `Configuration` 및 `OutputPath` 속성에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="5a8a9-154">다음 형식 중 하나를 사용하여 MSBuild 속성을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="5a8a9-155">예를 들어 다음 명령은 `Release` 빌드를 네트워크 공유에 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="5a8a9-156">네트워크 공유는 슬래시( *//r8/* )를 사용하여 지정하고 모든 .NET Core 지원 플랫폼에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-156">The network share is specified with forward slashes ( *//r8/* ) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="5a8a9-157">배포할 게시된 앱이 실행되고 있지 않은지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="5a8a9-158">앱이 실행 중이면 *publish* 폴더의 파일이 잠겨 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="5a8a9-159">잠긴 파일을 복사할 수 없으므로 배포를 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="5a8a9-160">게시 프로필</span><span class="sxs-lookup"><span data-stu-id="5a8a9-160">Publish profiles</span></span>

<span data-ttu-id="5a8a9-161">이 섹션에서는 Visual Studio 2019 이상을 사용하여 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="5a8a9-162">프로필이 만들어지면 Visual Studio 또는 명령줄에서 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="5a8a9-163">게시 프로필을 사용하면 게시 프로세스를 간소화할 수 있고 제한 없이 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="5a8a9-164">다음 경로 중 하나를 선택하여 Visual Studio에서 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="5a8a9-165">**솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="5a8a9-166">**빌드** 메뉴에서 **게시 {PROJECT NAME}** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="5a8a9-167">앱 기능 페이지의 **게시** 탭이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="5a8a9-168">프로젝트에 게시 프로필에 없는 경우 **게시 대상 선택** 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="5a8a9-169">다음 게시 대상 중 하나를 선택하라는 메시지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="5a8a9-170">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5a8a9-170">Azure App Service</span></span>
* <span data-ttu-id="5a8a9-171">Linux의 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5a8a9-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="5a8a9-172">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="5a8a9-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="5a8a9-173">폴더</span><span class="sxs-lookup"><span data-stu-id="5a8a9-173">Folder</span></span>
* <span data-ttu-id="5a8a9-174">IIS, FTP, 웹 배포(모든 웹 서버에 해당)</span><span class="sxs-lookup"><span data-stu-id="5a8a9-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="5a8a9-175">프로필 가져오기</span><span class="sxs-lookup"><span data-stu-id="5a8a9-175">Import Profile</span></span>

<span data-ttu-id="5a8a9-176">가장 적합한 게시 대상을 결정하려면 [내게 적합한 게시 옵션](/visualstudio/ide/not-in-toc/web-publish-options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="5a8a9-177">**폴더** 게시 대상을 선택하는 경우 게시된 자산을 저장할 폴더 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="5a8a9-178">기본 폴더 경로는 *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="5a8a9-179">예를 들어 *bin\Release\netcoreapp2.2\publish\\* 입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="5a8a9-180">**프로필 만들기** 단추를 선택하여 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="5a8a9-181">게시 프로필이 만들어지면 **게시** 탭의 콘텐츠가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="5a8a9-182">새로 만들어진 프로필이 드롭다운 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="5a8a9-183">드롭다운 목록 아래에서 **새 프로필 만들기** 를 선택하여 다른 새 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="5a8a9-184">Visual Studio의 게시 도구는 게시 프로필을 설명하는 *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="5a8a9-185">*.pubxml* 파일:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="5a8a9-186">게시 구성 설정을 포함하며 게시 프로세스에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="5a8a9-187">수정하여 빌드 및 게시 프로세스를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="5a8a9-188">Azure 대상에 게시하는 경우 *.pubxml* 파일에는 Azure 구독 식별자가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="5a8a9-189">대상 유형을 사용할 경우 이 파일을 소스 제어에 추가하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="5a8a9-190">Azure 이외 대상에 게시하는 경우 *.pubxml* 파일을 체크 인해도 안전합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="5a8a9-191">중요 정보(예: 게시 암호)는 사용자/컴퓨터 수준별로 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="5a8a9-192">이 정보는 *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* 파일에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="5a8a9-193">중요 정보가 저장될 수 있는 파일이므로 소스 제어에 체크 인하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="5a8a9-194">ASP.NET Core 웹앱을 게시하는 방법에 대한 개요를 보려면 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="5a8a9-195">ASP.NET Core 웹앱을 게시하는 데 필요한 MSBuild 작업 및 대상은 [dotnet/websdk 리포지토리](https://github.com/dotnet/websdk)에 있는 오픈 소스입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [dotnet/websdk repository](https://github.com/dotnet/websdk).</span></span>

<span data-ttu-id="5a8a9-196">다음 명령은 MSDeploy 폴더와 [Kudu](https://github.com/projectkudu/kudu/wiki) 게시 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="5a8a9-197">MSDeploy는 플랫폼 간 지원을 하지 않으므로 다음 MSDeploy 옵션은 Windows에서만 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="5a8a9-198">**폴더(플랫폼 간에 작동):**</span><span class="sxs-lookup"><span data-stu-id="5a8a9-198">**Folder (works cross-platform):**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="5a8a9-199">**MSDeploy:**</span><span class="sxs-lookup"><span data-stu-id="5a8a9-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="5a8a9-200">**MSDeploy 패키지:**</span><span class="sxs-lookup"><span data-stu-id="5a8a9-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="5a8a9-201">앞의 예에서</span><span class="sxs-lookup"><span data-stu-id="5a8a9-201">In the preceding examples:</span></span>

* <span data-ttu-id="5a8a9-202">`dotnet publish` 및 `dotnet build`는 모든 플랫폼에서 Azure에 게시할 Kudu API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="5a8a9-203">Visual Studio 게시는 Kudu API를 지원하지만 플랫폼 간 Azure에 게시에 대해 WebSDK에 의해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="5a8a9-204">`dotnet publish` 명령에 `DeployOnBuild`를 전달하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="5a8a9-205">자세한 내용은 [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="5a8a9-206">다음 콘텐츠와 함께 프로젝트의 *Properties/PublishProfiles* 폴더에 게시 프로필을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="5a8a9-207">폴더 게시 예</span><span class="sxs-lookup"><span data-stu-id="5a8a9-207">Folder publish example</span></span>

<span data-ttu-id="5a8a9-208">*FolderProfile* 이라는 프로필을 사용하여 게시할 경우 다음 명령 중 하나를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-208">When publishing with a profile named *FolderProfile* , use any of the following commands:</span></span>

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="5a8a9-209">.NET Core CLI의 [dotnet build](/dotnet/core/tools/dotnet-build) 명령은 `msbuild`를 호출하여 빌드 및 게시 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="5a8a9-210">폴더 프로필을 전달할 경우 `dotnet build` 및 `msbuild` 명령은 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="5a8a9-211">Windows에서 `msbuild`를 직접 호출하면 MSBuild의 .NET Framework 버전이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="5a8a9-212">폴더가 아닌 프로필에서 `dotnet build` 호출:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="5a8a9-213">MSDeploy를 사용하는 `msbuild`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="5a8a9-214">오류가 발생합니다(Windows에서 실행하는 경우에도).</span><span class="sxs-lookup"><span data-stu-id="5a8a9-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="5a8a9-215">폴더가 아닌 프로필을 사용하여 게시하려면 `msbuild`를 직접 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="5a8a9-216">다음 폴더 게시 프로필은 Visual Studio를 사용하여 만들어졌고 네트워크 공유에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="5a8a9-217">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-217">In the preceding example:</span></span>

* <span data-ttu-id="5a8a9-218">`<ExcludeApp_Data>` 속성은 XML 스키마 요구 사항을 충족하기 위해서만 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="5a8a9-219">`<ExcludeApp_Data>` 속성은 프로젝트 루트에 *App_Data* 폴더가 있는 경우에도 게시 프로세스에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="5a8a9-220">*App_Data* 폴더는 ASP.NET 4.x 프로젝트처럼 특별 처리를 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>
* <span data-ttu-id="5a8a9-221">`<LastUsedBuildConfiguration>` 속성은 `Release`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="5a8a9-222">Visual Studio에서 게시할 경우 `<LastUsedBuildConfiguration>`의 값은 게시 프로세스가 시작될 때의 값을 사용하여 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="5a8a9-223">`<LastUsedBuildConfiguration>`은 특별하고 가져온 MSBuild 파일에서 재정의되면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="5a8a9-224">그러나 이 속성은 다음 중 한 가지 접근 방법을 사용하여 명령줄에서 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="5a8a9-225">.NET Core CLI 사용:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="5a8a9-226">MSBuild 사용:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-226">Using MSBuild:</span></span>

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="5a8a9-227">자세한 내용은 [MSBuild: 구성 속성을 설정하는 방법](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="5a8a9-228">명령줄에서 MSDeploy 엔드포인트에 게시</span><span class="sxs-lookup"><span data-stu-id="5a8a9-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="5a8a9-229">다음 예제에서는 *AzureWebApp* 라는 Visual Studio에서 만든 ASP.NET Core 웹앱을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="5a8a9-230">Azure 게시 프로필이 Visual Studio에 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="5a8a9-231">프로필을 만드는 방법에 대한 자세한 내용은 [게시 프로필](#publish-profiles) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="5a8a9-232">게시 프로필을 사용하여 앱을 배포하려면 Visual Studio **개발자 명령 프롬프트** 에서 `msbuild` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="5a8a9-233">명령 프롬프트는 Windows 작업 표시줄의 **시작** 메뉴에 있는 *Visual Studio* 폴더에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="5a8a9-234">쉽게 액세스할 수 있도록 Visual Studio의 **도구** 메뉴에 명령 프롬프트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="5a8a9-235">자세한 내용은 [Visual Studio용 개발자 명령 프롬프트](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="5a8a9-236">MSBuild는 다음 명령 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-236">MSBuild uses the following command syntax:</span></span>

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="5a8a9-237">`{PATH}`: 앱의 프로젝트 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-237">`{PATH}`: Path to the app's project file.</span></span>
* <span data-ttu-id="5a8a9-238">`{PROFILE}`: 게시 프로필의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-238">`{PROFILE}`: Name of the publish profile.</span></span>
* <span data-ttu-id="5a8a9-239">`{USERNAME}`: MSDeploy 사용자 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-239">`{USERNAME}`: MSDeploy username.</span></span> <span data-ttu-id="5a8a9-240">`{USERNAME}`은 게시 프로필에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-240">The `{USERNAME}` can be found in the publish profile.</span></span>
* <span data-ttu-id="5a8a9-241">`{PASSWORD}`: MSDeploy 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-241">`{PASSWORD}`: MSDeploy password.</span></span> <span data-ttu-id="5a8a9-242">*{PROFILE}.PublishSettings* 파일에서 `{PASSWORD}`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-242">Obtain the `{PASSWORD}` from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="5a8a9-243">다음 위치에서 *.PublishSettings* 파일을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="5a8a9-244">**솔루션 탐색기** : **보기** > **클라우드 탐색기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-244">**Solution Explorer** : Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="5a8a9-245">Azure 구독으로 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="5a8a9-246">**App Services** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-246">Open **App Services**.</span></span> <span data-ttu-id="5a8a9-247">앱을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-247">Right-click the app.</span></span> <span data-ttu-id="5a8a9-248">**게시 프로필 다운로드** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="5a8a9-249">Azure Portal: 웹앱의 **개요** 패널에서 **게시 프로필 가져오기** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="5a8a9-250">다음 예제에서는 *AzureWebApp - 웹 배포* 라는 게시 프로필을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-250">The following example uses a publish profile named *AzureWebApp - Web Deploy* :</span></span>

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="5a8a9-251">게시 프로필은 Windows 명령 셸에서 .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 명령과 함께 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="5a8a9-252">`dotnet msbuild` 명령은 플랫폼 간 명령이며 macOS 및 Linux에서 ASP.NET Core 앱을 컴파일할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="5a8a9-253">그러나 macOS 및 Linux의 MSBuild는 Azure 또는 다른 MSDeploy 엔드포인트에 앱을 배포할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="5a8a9-254">환경 설정</span><span class="sxs-lookup"><span data-stu-id="5a8a9-254">Set the environment</span></span>

<span data-ttu-id="5a8a9-255">`<EnvironmentName>` 속성을 게시 프로필( *.pubxml* ) 또는 프로젝트 파일에 포함하여 앱의 [환경](xref:fundamentals/environments)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-255">Include the `<EnvironmentName>` property in the publish profile ( *.pubxml* ) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="5a8a9-256">*web.config* 변환(예: 구성, 프로필 또는 환경에 따라 환경 변수 설정)이 필요한 경우 <xref:host-and-deploy/iis/transform-webconfig>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="5a8a9-257">파일 제외</span><span class="sxs-lookup"><span data-stu-id="5a8a9-257">Exclude files</span></span>

<span data-ttu-id="5a8a9-258">ASP.NET Core 웹앱을 게시하는 경우 다음 자산이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="5a8a9-259">빌드 아티팩트</span><span class="sxs-lookup"><span data-stu-id="5a8a9-259">Build artifacts</span></span>
* <span data-ttu-id="5a8a9-260">다음 와일드카드 사용 패턴과 일치하는 폴더 및 파일:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="5a8a9-261">`**\*.config`(예: *web.config* )</span><span class="sxs-lookup"><span data-stu-id="5a8a9-261">`**\*.config` (for example, *web.config* )</span></span>
  * <span data-ttu-id="5a8a9-262">`**\*.json`(예를 들어 *appsettings.json* )</span><span class="sxs-lookup"><span data-stu-id="5a8a9-262">`**\*.json` (for example, *appsettings.json* )</span></span>
  * `wwwroot\**`

<span data-ttu-id="5a8a9-263">MSBuild는 [와일드카드 사용 패턴](https://gruntjs.com/configuring-tasks#globbing-patterns)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="5a8a9-264">예를 들어 다음 `<Content>` 요소는 *wwwroot\content* 폴더와 모든 하위 폴더에 있는 텍스트( *.txt* ) 파일의 복사를 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-264">For example, the following `<Content>` element suppresses the copying of text ( *.txt* ) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="5a8a9-265">앞의 태그는 게시 프로필 또는 *.csproj* 파일에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="5a8a9-266">*.csproj* 파일에 추가된 규칙은 프로젝트의 모든 게시 프로필에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="5a8a9-267">다음 `<MsDeploySkipRules>` 요소는 *wwwroot\content* 폴더의 모든 파일을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="5a8a9-268">`<MsDeploySkipRules>`는 배포 사이트에서 ‘건너뛰기’ 대상을 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="5a8a9-269">`<Content>` 대상 파일 및 폴더는 배포 사이트에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="5a8a9-270">예를 들어 배포된 웹앱에 다음 파일이 포함되었다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="5a8a9-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5a8a9-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="5a8a9-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5a8a9-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="5a8a9-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5a8a9-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="5a8a9-274">다음 `<MsDeploySkipRules>` 요소가 추가되면 해당 파일은 배포 사이트에서 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="5a8a9-275">앞의 `<MsDeploySkipRules>` 요소는 ‘건너뛴’ 파일이 배포되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="5a8a9-276">해당 파일은 배포된 후에 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="5a8a9-277">다음 `<Content>` 요소는 배포 사이트에서 대상 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="5a8a9-278">앞의 `<Content>` 요소와 함께 명령줄 배포를 사용하면 다음 출력의 변형이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="5a8a9-279">포함 파일</span><span class="sxs-lookup"><span data-stu-id="5a8a9-279">Include files</span></span>

<span data-ttu-id="5a8a9-280">다음 섹션에서는 게시 시점에서의 파일 포함을 위한 여러 가지 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="5a8a9-281">[일반 파일 포함](#general-file-inclusion) 섹션에서는 [웹 SDK](xref:razor-pages/web-sdk)의 게시 대상 파일이 제공하는 `DotNetPublishFiles` 항목을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="5a8a9-282">[선택적 파일 포함](#selective-file-inclusion) 섹션에서는 [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)의 게시 대상 파일이 제공하는 `ResolvedFileToPublish` 항목을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="5a8a9-283">Web SDK는 .NET Core SDK를 사용하기 때문에 ASP.NET Core 프로젝트에서 항목 중 하나를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="5a8a9-284">일반 파일 포함</span><span class="sxs-lookup"><span data-stu-id="5a8a9-284">General file inclusion</span></span>

<span data-ttu-id="5a8a9-285">다음 예제의 `<ItemGroup>` 요소는 프로젝트 디렉터리 밖에 위치한 폴더를 게시된 사이트의 폴더로 복사하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="5a8a9-286">다음 변경 내용의 `<ItemGroup>`에 추가된 모든 파일이 기본적으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="5a8a9-287">위의 표시는:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-287">The preceding markup:</span></span>

* <span data-ttu-id="5a8a9-288">*.csproj* 파일 또는 게시 프로필에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="5a8a9-289">태그가 *.csproj* 파일에 추가되면 프로젝트의 각 게시 프로필에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="5a8a9-290">`Include` 특성의 GLOB 패턴과 일치하는 파일을 저장할 `_CustomFiles` 항목을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="5a8a9-291">패턴에서 참조된 *images* 폴더는 프로젝트 디렉터리 밖에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="5a8a9-292">이름이 `$(MSBuildProjectDirectory)`인 [예약된 속성](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)은 프로젝트 파일의 절대 경로로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="5a8a9-293">파일 목록을 `DotNetPublishFiles` 항목에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="5a8a9-294">기본적으로 항목의 `<DestinationRelativePath>` 요소는 비어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="5a8a9-295">기본값은 변경 내용에서 재정의되며 `%(RecursiveDir)`과 같이 [잘 알려진 항목 메타데이터](/visualstudio/msbuild/msbuild-well-known-item-metadata)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="5a8a9-296">내부 텍스트는 게시된 사이트의 *wwwroot/images* 폴더를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="5a8a9-297">선택적 파일 포함</span><span class="sxs-lookup"><span data-stu-id="5a8a9-297">Selective file inclusion</span></span>

<span data-ttu-id="5a8a9-298">다음 예제에서 강조 표시된 변경 내용은 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="5a8a9-299">프로젝트 밖에 위치한 파일을 게시된 사이트의 *wwwroot* 폴더로 복사.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="5a8a9-300">*ReadMe2.md* 의 파일 이름은 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="5a8a9-301">*wwwroot\Content* 폴더 제외.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="5a8a9-302">*Views\Home\About2.cshtml* 제외.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="5a8a9-303">앞의 예제에서는 `Include` 특성에 제공된 파일을 항상 게시된 사이트로 복사하는 것이 기본 동작인 `ResolvedFileToPublish` 항목을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="5a8a9-304">`Never` 또는 `PreserveNewest`의 내부 텍스트와 함께 `<CopyToPublishDirectory>` 자식 요소를 포함하여 기본 동작을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="5a8a9-305">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="5a8a9-306">더 많은 배포 샘플을 보려면 [웹 SDK 추가 정보 파일](https://github.com/dotnet/sdk/tree/master/src/WebSdk)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-306">For more deployment samples, see the [Web SDK README file](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="5a8a9-307">게시 이전 또는 이후 대상 실행</span><span class="sxs-lookup"><span data-stu-id="5a8a9-307">Run a target before or after publishing</span></span>

<span data-ttu-id="5a8a9-308">기본 제공 `BeforePublish` 및 `AfterPublish` 대상은 게시 대상 이전 또는 이후에 대상을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="5a8a9-309">다음 요소를 게시 프로필에 추가하여 게시 이전 및 이후에 콘솔 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="5a8a9-310">신뢰할 수 없는 인증서를 사용하여 서버에 게시</span><span class="sxs-lookup"><span data-stu-id="5a8a9-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="5a8a9-311">`True` 값을 사용하는 `<AllowUntrustedCertificate>` 속성을 게시 프로필에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="5a8a9-312">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="5a8a9-312">The Kudu service</span></span>

<span data-ttu-id="5a8a9-313">Azure App Service 웹앱 배포에 있는 파일을 보려면 [Kudu 서비스](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="5a8a9-314">`scm` 토큰을 웹앱 이름에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="5a8a9-315">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="5a8a9-315">For example:</span></span>

| <span data-ttu-id="5a8a9-316">URL</span><span class="sxs-lookup"><span data-stu-id="5a8a9-316">URL</span></span>                                    | <span data-ttu-id="5a8a9-317">결과</span><span class="sxs-lookup"><span data-stu-id="5a8a9-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="5a8a9-318">웹앱</span><span class="sxs-lookup"><span data-stu-id="5a8a9-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="5a8a9-319">Kudu 서비스</span><span class="sxs-lookup"><span data-stu-id="5a8a9-319">Kudu service</span></span> |

<span data-ttu-id="5a8a9-320">[디버그 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 메뉴 항목을 선택하여 파일을 표시, 편집, 삭제 또는 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a8a9-321">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5a8a9-321">Additional resources</span></span>

* <span data-ttu-id="5a8a9-322">[웹 배포](https://www.iis.net/downloads/microsoft/web-deploy)(MSDeploy)는 IIS 서버에 대한 웹앱 및 웹 사이트 배포를 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="5a8a9-323">[웹 SDK GitHub 리포지토리](https://github.com/dotnet/websdk/issues): 배포에 대한 파일 문제 및 요청 기능.</span><span class="sxs-lookup"><span data-stu-id="5a8a9-323">[Web SDK GitHub repository](https://github.com/dotnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="5a8a9-324">Visual Studio에서 Azure VM에 ASP.NET 웹앱 게시</span><span class="sxs-lookup"><span data-stu-id="5a8a9-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
