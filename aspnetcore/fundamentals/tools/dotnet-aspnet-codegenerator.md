---
title: dotnet aspnet-codegenerator 명령
author: rick-anderson
description: dotnet aspnet codegenerator 명령은 ASP.NET Core 프로젝트를 스캐폴딩합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: d29535ea0b4197443fd884aaa6e5b4b763d04fc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920705"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="aa656-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="aa656-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="aa656-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aa656-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aa656-105">`dotnet aspnet-codegenerator`는 ASP.NET Core 스캐폴딩 엔진을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="aa656-106">`dotnet aspnet-codegenerator`는 명령줄에서 스캐폴딩하는 경우에만 필요하며, Visual Studio에서 스캐폴딩을 사용하는 경우에는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="aa656-107">Install and update aspnet-codegenerator 설치 및 업데이트</span><span class="sxs-lookup"><span data-stu-id="aa656-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="aa656-108">[.NET SDK](https://dotnet.microsoft.com/download)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="aa656-109">`dotnet-aspnet-codegenerator`는 설치가 필요한 [전역 도구](/dotnet/core/tools/global-tools)입니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="aa656-110">다음 명령은 `dotnet-aspnet-codegenerator` 도구의 안정적인 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="aa656-111">다음 명령은 `dotnet-aspnet-codegenerator`를 설치된 .NET Core SDK에서 사용 가능한 안정적인 최신 버전으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="aa656-112">aspnet-codegenerator 제거</span><span class="sxs-lookup"><span data-stu-id="aa656-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="aa656-113">문제를 해결하려면 `aspnet-codegenerator`를 제거해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="aa656-114">예를 들어, `aspnet-codegenerator`의 미리 보기 버전을 설치한 경우 릴리스 버전을 설치하기 전에 제거해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="aa656-115">다음 명령은 `dotnet-aspnet-codegenerator` 도구를 제거하고 안정적인 최신 버전을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="aa656-116">개요</span><span class="sxs-lookup"><span data-stu-id="aa656-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="aa656-117">설명</span><span class="sxs-lookup"><span data-stu-id="aa656-117">Description</span></span>

<span data-ttu-id="aa656-118">`dotnet aspnet-codegenerator` 전역 명령은 ASP.NET Core 코드 생성기 및 스캐폴딩 엔진을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="aa656-119">인수</span><span class="sxs-lookup"><span data-stu-id="aa656-119">Arguments</span></span>

`generator`

<span data-ttu-id="aa656-120">실행할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-120">The code generator to run.</span></span> <span data-ttu-id="aa656-121">다음 생성기를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-121">The following generators are available:</span></span>

| <span data-ttu-id="aa656-122">생성기</span><span class="sxs-lookup"><span data-stu-id="aa656-122">Generator</span></span>  | <span data-ttu-id="aa656-123">연산</span><span class="sxs-lookup"><span data-stu-id="aa656-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="aa656-124">area</span><span class="sxs-lookup"><span data-stu-id="aa656-124">area</span></span>       | [<span data-ttu-id="aa656-125">영역 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="aa656-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="aa656-126">Controller</span><span class="sxs-lookup"><span data-stu-id="aa656-126">controller</span></span> | [<span data-ttu-id="aa656-127">컨트롤러 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="aa656-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="aa656-128">identity</span><span class="sxs-lookup"><span data-stu-id="aa656-128">identity</span></span>   | [<span data-ttu-id="aa656-129">Identity 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="aa656-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="aa656-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="aa656-130">razorpage</span></span>  | [<span data-ttu-id="aa656-131">Razor Pages 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="aa656-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="aa656-132">view</span><span class="sxs-lookup"><span data-stu-id="aa656-132">view</span></span>       | [<span data-ttu-id="aa656-133">보기 스캐폴딩</span><span class="sxs-lookup"><span data-stu-id="aa656-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="aa656-134">옵션</span><span class="sxs-lookup"><span data-stu-id="aa656-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="aa656-135">NuGet 패키지 디렉터리를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="aa656-136">빌드 구성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-136">Defines the build configuration.</span></span> <span data-ttu-id="aa656-137">기본값은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="aa656-138">사용할 대상 [프레임워크](/dotnet/standard/frameworks)입니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="aa656-139">예: `net46`.</span><span class="sxs-lookup"><span data-stu-id="aa656-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="aa656-140">빌드 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="aa656-141">명령에 대한 간단한 도움말을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="aa656-142">실행하기 전에 프로젝트를 빌드하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-142">Doesn't build the project before running.</span></span> <span data-ttu-id="aa656-143">또한 암시적으로 `--no-restore` 플래그를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="aa656-144">실행할 프로젝트 파일의 경로를 지정합니다(폴더 이름 또는 전체 경로).</span><span class="sxs-lookup"><span data-stu-id="aa656-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="aa656-145">지정하지 않으면 현재 디렉터리로 기본 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="aa656-146">생성기 옵션</span><span class="sxs-lookup"><span data-stu-id="aa656-146">Generator options</span></span>

<span data-ttu-id="aa656-147">다음 섹션에서는 지원되는 생성기에 사용 가능한 옵션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="aa656-148">Area</span><span class="sxs-lookup"><span data-stu-id="aa656-148">Area</span></span>
* <span data-ttu-id="aa656-149">Controller</span><span class="sxs-lookup"><span data-stu-id="aa656-149">Controller</span></span>
* Identity  
* <span data-ttu-id="aa656-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="aa656-150">Razorpage</span></span>
* <span data-ttu-id="aa656-151">View</span><span class="sxs-lookup"><span data-stu-id="aa656-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="aa656-152">Area 옵션</span><span class="sxs-lookup"><span data-stu-id="aa656-152">Area options</span></span>

<span data-ttu-id="aa656-153">이 도구는 컨트롤러와 보기가 포함된 ASP.NET Core 웹 프로젝트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="aa656-154">Razor Pages 앱에는 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="aa656-155">사용법: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="aa656-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="aa656-156">앞의 명령은 다음 폴더를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="aa656-157">*Areas*</span><span class="sxs-lookup"><span data-stu-id="aa656-157">*Areas*</span></span>
  * <span data-ttu-id="aa656-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="aa656-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="aa656-159">*컨트롤러*</span><span class="sxs-lookup"><span data-stu-id="aa656-159">*Controllers*</span></span>
    * <span data-ttu-id="aa656-160">*Data*</span><span class="sxs-lookup"><span data-stu-id="aa656-160">*Data*</span></span>
    * <span data-ttu-id="aa656-161">*Models*</span><span class="sxs-lookup"><span data-stu-id="aa656-161">*Models*</span></span>
    * <span data-ttu-id="aa656-162">*Views*</span><span class="sxs-lookup"><span data-stu-id="aa656-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="aa656-163">Controller 옵션</span><span class="sxs-lookup"><span data-stu-id="aa656-163">Controller options</span></span>

<span data-ttu-id="aa656-164">다음 표는 `aspnet-codegenerator` `controller` 및 `razorpage`의 옵션 목록을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="aa656-165">다음 표는 `aspnet-codegenerator controller`에 고유한 옵션 목록을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="aa656-166">옵션</span><span class="sxs-lookup"><span data-stu-id="aa656-166">Option</span></span>                         | <span data-ttu-id="aa656-167">설명</span><span class="sxs-lookup"><span data-stu-id="aa656-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="aa656-168">--controllerName 또는 -name</span><span class="sxs-lookup"><span data-stu-id="aa656-168">--controllerName or -name</span></span>      | <span data-ttu-id="aa656-169">컨트롤러의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="aa656-170">--useAsyncActions 또는 -async</span><span class="sxs-lookup"><span data-stu-id="aa656-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="aa656-171">비동기 컨트롤러 작업을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="aa656-172">--noViews 또는 -nv</span><span class="sxs-lookup"><span data-stu-id="aa656-172">--noViews or -nv</span></span>               | <span data-ttu-id="aa656-173">보기를 생성하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="aa656-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="aa656-174">--restWithNoViews 또는 -api</span><span class="sxs-lookup"><span data-stu-id="aa656-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="aa656-175">REST 스타일 API를 사용하여 컨트롤러를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="aa656-176">`noViews` 옵션이 지정된 것으로 간주되며 모든 보기 관련된 옵션이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="aa656-177">--readWriteActions 또는 -actions</span><span class="sxs-lookup"><span data-stu-id="aa656-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="aa656-178">모델 없이 읽기/쓰기 동작이 포함된 컨트롤러를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="aa656-179">`-h` 스위치를 사용하여 `aspnet-codegenerator controller` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="aa656-180">`dotnet aspnet-codegenerator controller`의 예제는 [동영상 모델 스캐폴드](xref:tutorials/first-mvc-app/adding-model)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa656-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="aa656-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="aa656-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="aa656-182">Razor Pages는 새 페이지 이름 및 사용할 템플릿을 지정하여 개별적으로 스캐폴드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="aa656-183">지원되는 템플릿은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="aa656-184">예를 들어 다음 명령은 Edit 템플릿을 사용하여 *MyEdit.cshtml* 및 *MyEdit.cshtml.cs* 를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="aa656-185">일반적으로 템플릿 및 생성된 파일 이름은 지정되지 않으며 다음 템플릿이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="aa656-186">다음 표는 `aspnet-codegenerator` `razorpage` 및 `controller`의 옵션 목록을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="aa656-187">다음 표는 `aspnet-codegenerator razorpage`에 고유한 옵션 목록을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="aa656-188">옵션</span><span class="sxs-lookup"><span data-stu-id="aa656-188">Option</span></span>                        | <span data-ttu-id="aa656-189">설명</span><span class="sxs-lookup"><span data-stu-id="aa656-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="aa656-190">--namespaceName 또는 -namespace</span><span class="sxs-lookup"><span data-stu-id="aa656-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="aa656-191">생성된 PageModel에 사용할 네임스페이스의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="aa656-192">--partialView 또는 -partial</span><span class="sxs-lookup"><span data-stu-id="aa656-192">--partialView or -partial</span></span>     | <span data-ttu-id="aa656-193">부분 보기를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-193">Generate a partial view.</span></span> <span data-ttu-id="aa656-194">이 옵션이 지정되면 레이아웃 옵션 -l 및 -udl은 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="aa656-195">--noPageModel 또는 -npm</span><span class="sxs-lookup"><span data-stu-id="aa656-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="aa656-196">빈 템플릿에 대한 PageModel 클래스를 생성하지 않도록 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="aa656-197">`-h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="aa656-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="aa656-198">`dotnet aspnet-codegenerator razorpage`의 예제는 [동영상 모델 스캐폴드](xref:tutorials/razor-pages/model)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa656-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="aa656-199">[스캐폴드Identity](xref:security/authentication/scaffold-identity)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="aa656-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
