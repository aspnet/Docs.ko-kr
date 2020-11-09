---
title: 파일 감시자를 사용하여 ASP.NET Core 앱 개발
author: rick-anderson
description: 이 자습서에서는 ASP.NET Core 앱에서 .NET Core CLI 파일 감시자(dotnet 조사식) 도구를 사용하는 방법을 보여줍니다.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060042"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="3ab63-103">파일 감시자를 사용하여 ASP.NET Core 앱 개발</span><span class="sxs-lookup"><span data-stu-id="3ab63-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="3ab63-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="3ab63-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="3ab63-105">`dotnet watch`은 원본 파일을 변경할 때 [.NET Core CLI](/dotnet/core/tools) 명령을 실행하는 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="3ab63-106">예를 들어 파일 변경은 컴파일, 테스트 실행 또는 배포를 트리거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="3ab63-107">이 자습서에서는 합계를 반환하는 엔드포인트 및 제품을 반환하는 엔드포인트 등 두 개의 엔드포인트에서 기존 Web API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="3ab63-108">제품 메서드에는 버그가 있습니다. 이 문제가 이 자습서에서 해결되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="3ab63-109">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)을 다운로드합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="3ab63-110">다음 두 프로젝트로 구성되어 있습니다. *WebApp* (ASP.NET Core 웹 API) 및 *WebAppTests* (웹 API의 단위 테스트).</span><span class="sxs-lookup"><span data-stu-id="3ab63-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="3ab63-111">명령 셸에서 *WebApp* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="3ab63-112">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="3ab63-113">`dotnet run --project <PROJECT>`를 사용하여 실행할 프로젝트를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="3ab63-114">예를 들어 샘플 앱의 루트에서 `dotnet run --project WebApp`을 실행하면 *WebApp* 프로젝트도 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="3ab63-115">콘솔 출력은 다음과 유사한 메시지를 표시합니다(앱이 실행되고 요청을 대기하는 것을 나타냄).</span><span class="sxs-lookup"><span data-stu-id="3ab63-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="3ab63-116">웹 브라우저에서 `http://localhost:<port number>/api/math/sum?a=4&b=5`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="3ab63-117">`9`라는 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-117">You should see the result of `9`.</span></span>

<span data-ttu-id="3ab63-118">API 제품으로 이동합니다(`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="3ab63-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="3ab63-119">예상한 대로 `20`이 아니라 `9`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="3ab63-120">이러한 문제는이 자습서의 뒷부분에서 해결되었습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="3ab63-121">프로젝트에 `dotnet watch` 추가</span><span class="sxs-lookup"><span data-stu-id="3ab63-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="3ab63-122">`dotnet watch` 파일 감시자 도구는 .NET Core SDK 버전 2.1.300에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="3ab63-123">다음 단계는 이전 버전의 .NET Core SDK를 사용하는 경우에 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="3ab63-124">`Microsoft.DotNet.Watcher.Tools` 패키지 참조를 *.csproj* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="3ab63-125">다음 명령을 실행하여 `Microsoft.DotNet.Watcher.Tools` 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="3ab63-126">`dotnet watch`를 사용하여 .NET Core CLI 명령 실행</span><span class="sxs-lookup"><span data-stu-id="3ab63-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="3ab63-127">모든 [.NET Core CLI 명령](/dotnet/core/tools#cli-commands)을 `dotnet watch`로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="3ab63-128">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="3ab63-128">For example:</span></span>

| <span data-ttu-id="3ab63-129">명령</span><span class="sxs-lookup"><span data-stu-id="3ab63-129">Command</span></span> | <span data-ttu-id="3ab63-130">watch를 사용하는 명령</span><span class="sxs-lookup"><span data-stu-id="3ab63-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="3ab63-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="3ab63-131">dotnet run</span></span> | <span data-ttu-id="3ab63-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="3ab63-132">dotnet watch run</span></span> |
| <span data-ttu-id="3ab63-133">dotnet run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="3ab63-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="3ab63-134">dotnet watch run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="3ab63-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="3ab63-135">dotnet run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="3ab63-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="3ab63-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="3ab63-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="3ab63-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="3ab63-137">dotnet test</span></span> | <span data-ttu-id="3ab63-138">dotnet watch 테스트</span><span class="sxs-lookup"><span data-stu-id="3ab63-138">dotnet watch test</span></span> |

<span data-ttu-id="3ab63-139">*WebApp* 폴더에서 `dotnet watch run`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="3ab63-140">콘솔 출력은 `watch`가 시작했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="3ab63-141">웹앱에서 `dotnet watch run`을 실행하면 준비된 앱의 URL로 이동하는 브라우저가 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="3ab63-142">`dotnet watch`는 앱의 콘솔 출력값을 읽고 <xref:Microsoft.AspNetCore.WebHost>에서 표시하는 준비 메시지를 대기하여 이 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="3ab63-143">`dotnet watch`는 감시된 파일의 변경 내용을 검색할 때 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="3ab63-144">이렇게 하려면 조사식 명령이 앱에서 만든 HTML 응답을 수정하는 미들웨어를 앱에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="3ab63-145">미들웨어는 `dotnet watch`를 허용하는 JavaScript 스크립트 블록을 페이지에 추가하여 브라우저를 새로 고치도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="3ab63-146">현재 *.html* 및 *.css* 파일과 같은 정적 콘텐츠를 포함하여 모든 감시된 파일이 변경되면 앱이 다시 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="3ab63-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="3ab63-147">`dotnet watch`:</span></span>

* <span data-ttu-id="3ab63-148">기본적으로 빌드에 영향을 주는 파일만 감시합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="3ab63-149">구성을 통해 추가로 감시된 파일이 있으면 빌드가 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="3ab63-150">구성에 대한 자세한 내용은 이 문서의 [dotnet-watch 구성](#dotnet-watch-configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3ab63-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3ab63-151">`dotnet watch --project <PROJECT>`를 사용하여 감시할 프로젝트를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="3ab63-152">예를 들어 샘플 앱의 루트에서 `dotnet watch --project WebApp run`을 실행하면 *WebApp* 프로젝트도 실행 및 감시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="3ab63-153">`dotnet watch` 변경</span><span class="sxs-lookup"><span data-stu-id="3ab63-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="3ab63-154">`dotnet watch`가 실행되고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="3ab63-155">합계가 아닌 제품을 반환하도록 *MathController.cs* 의 `Product` 메서드에서 버그를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="3ab63-156">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-156">Save the file.</span></span> <span data-ttu-id="3ab63-157">콘솔 출력은 `dotnet watch`에서 파일 변경을 검색했고 앱을 다시 시작했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="3ab63-158">`http://localhost:<port number>/api/math/product?a=4&b=5`에서 올바른 결과를 반환하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="3ab63-159">`dotnet watch`를 사용하여 테스트 실행</span><span class="sxs-lookup"><span data-stu-id="3ab63-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="3ab63-160">*MathController.cs* 의 `Product` 메서드가 합계를 반환하도록 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="3ab63-161">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-161">Save the file.</span></span>
1. <span data-ttu-id="3ab63-162">명령 셸에서 *WebAppTests* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="3ab63-163">[dotnet restore](/dotnet/core/tools/dotnet-restore)를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="3ab63-164">`dotnet watch test`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="3ab63-165">이 출력은 테스트에 실패했으며 감시자가 파일 변경을 대기 중임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="3ab63-166">제품을 반환하도록 `Product` 메서드 코드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="3ab63-167">파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-167">Save the file.</span></span>

<span data-ttu-id="3ab63-168">`dotnet watch`는 파일 변경을 검색하고 테스트를 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="3ab63-169">콘솔 출력은 통과된 테스트를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="3ab63-170">조사할 파일 목록 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="3ab63-170">Customize files list to watch</span></span>

<span data-ttu-id="3ab63-171">기본적으로 `dotnet-watch`는 다음 GLOB 패턴과 일치하는 모든 파일을 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="3ab63-172">*.csproj* 파일을 편집하여 조사식 목록에 더 많은 항목을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="3ab63-173">개별적으로 또는 GLOB 패턴을 사용하여 항목을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="3ab63-174">감시할 파일 옵트아웃</span><span class="sxs-lookup"><span data-stu-id="3ab63-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="3ab63-175">기본 설정을 무시하도록 `dotnet-watch`를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="3ab63-176">특정 파일을 무시하려면 *.csproj* 파일에서 `Watch="false"` 특성을 항목의 정의로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="3ab63-177">사용자 정의 조사식 프로젝트</span><span class="sxs-lookup"><span data-stu-id="3ab63-177">Custom watch projects</span></span>

<span data-ttu-id="3ab63-178">`dotnet-watch`는 C# 프로젝트로 제한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="3ab63-179">다른 시나리오를 처리하도록 사용자 지정 조사식 프로젝트를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="3ab63-180">다음 프로젝트 레이아웃을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-180">Consider the following project layout:</span></span>

* <span data-ttu-id="3ab63-181">**test/**</span><span class="sxs-lookup"><span data-stu-id="3ab63-181">**test/**</span></span>
  * <span data-ttu-id="3ab63-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="3ab63-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="3ab63-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="3ab63-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="3ab63-184">두 프로젝트를 조사하는 것이 목적인 경우 두 프로젝트를 감시하도록 구성된 사용자 지정 프로젝트 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="3ab63-185">파일이 두 프로젝트를 감시하는 시작하려면 *test* 폴더를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="3ab63-186">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="3ab63-187">테스트 프로젝트 중 하나에서 파일을 변경하면 VSTest가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="3ab63-188">dotnet-watch 구성</span><span class="sxs-lookup"><span data-stu-id="3ab63-188">dotnet-watch configuration</span></span>

<span data-ttu-id="3ab63-189">환경 변수를 통해 일부 구성 옵션을 `dotnet watch`에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="3ab63-190">사용 가능한 변수는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-190">The available variables are:</span></span>

| <span data-ttu-id="3ab63-191">설정</span><span class="sxs-lookup"><span data-stu-id="3ab63-191">Setting</span></span>  | <span data-ttu-id="3ab63-192">설명</span><span class="sxs-lookup"><span data-stu-id="3ab63-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="3ab63-193">“1” 또는 “true”로 설정하면 `dotnet watch`에서 CoreFx의 `FileSystemWatcher` 대신 폴링 파일 감시자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="3ab63-194">네트워크 공유 또는 Docker 탑재 볼륨에서 파일을 감시할 때 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="3ab63-195">기본적으로 `dotnet watch`는 파일이 변경될 때마다 복원 실행이나 감시된 파일 집합 다시 평가와 같은 특정 작업이 수행되지 않도록 하여 빌드를 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="3ab63-196">“1” 또는 “true”로 설정하면 이러한 최적화를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="3ab63-197">`dotnet watch run`은 *launchSettings.json* 에서 `launchBrowser`가 구성된 웹앱에 대해 브라우저를 시작하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="3ab63-198">“1” 또는 “true”로 설정하면 이 동작이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="3ab63-199">`dotnet watch run`은 파일 변경 내용을 검색할 때 브라우저를 새로 고치려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="3ab63-200">“1” 또는 “true”로 설정하면 이 동작이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="3ab63-201">이 동작은 `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`가 설정된 경우에도 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="3ab63-202">GitHub의 `dotnet-watch`</span><span class="sxs-lookup"><span data-stu-id="3ab63-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="3ab63-203">`dotnet-watch`는 GitHub [dotnet/AspNetCore 리포지토리](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="3ab63-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
