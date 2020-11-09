---
title: OpenAPI를 사용한 ASP.NET Core 앱 개발
author: ryanbrandenburg
description: "'Microsoft dotnet-openapi' 도구를 사용하여 OpenAPI 파일에 참조를 추가하는 방법을 보여줍니다."
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 28a71c7040667c7544cc17c1184c09b5b39959b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052554"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="48d1a-103">OpenAPI 도구를 사용한 ASP.NET Core 앱 개발</span><span class="sxs-lookup"><span data-stu-id="48d1a-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="48d1a-104">Ryan Brandenburg가 개발</span><span class="sxs-lookup"><span data-stu-id="48d1a-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="48d1a-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi)은 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 참조를 관리하기 위한 [.NET Core 전역 도구](/dotnet/core/tools/global-tools)입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="48d1a-106">설치</span><span class="sxs-lookup"><span data-stu-id="48d1a-106">Installation</span></span>

<span data-ttu-id="48d1a-107">`Microsoft.dotnet-openapi`를 설치하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="48d1a-108">추가</span><span class="sxs-lookup"><span data-stu-id="48d1a-108">Add</span></span>

<span data-ttu-id="48d1a-109">이 페이지의 명령 중 하나를 사용하여 OpenAPI 참조를 추가하면 다음과 유사한 `<OpenApiReference />` 요소가 *.csproj* 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="48d1a-110">생성된 클라이언트 코드를 앱에서 호출하려면 이전 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="48d1a-111">파일 추가</span><span class="sxs-lookup"><span data-stu-id="48d1a-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="48d1a-112">옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-112">Options</span></span>

| <span data-ttu-id="48d1a-113">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-113">Short option</span></span>| <span data-ttu-id="48d1a-114">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-114">Long option</span></span>| <span data-ttu-id="48d1a-115">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-115">Description</span></span> | <span data-ttu-id="48d1a-116">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="48d1a-117">-p</span><span class="sxs-lookup"><span data-stu-id="48d1a-117">-p</span></span>|<span data-ttu-id="48d1a-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="48d1a-118">--updateProject</span></span> | <span data-ttu-id="48d1a-119">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-119">The project to operate on.</span></span> |<span data-ttu-id="48d1a-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="48d1a-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="48d1a-121">-c</span><span class="sxs-lookup"><span data-stu-id="48d1a-121">-c</span></span>|<span data-ttu-id="48d1a-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="48d1a-122">--code-generator</span></span>| <span data-ttu-id="48d1a-123">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="48d1a-124">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="48d1a-125">`--code-generator`가 지정되지 않으면 도구는 기본적으로 `NSwagCSharp`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="48d1a-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="48d1a-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="48d1a-127">-H</span><span class="sxs-lookup"><span data-stu-id="48d1a-127">-h</span></span>|<span data-ttu-id="48d1a-128">--help</span><span class="sxs-lookup"><span data-stu-id="48d1a-128">--help</span></span>|<span data-ttu-id="48d1a-129">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-129">Show help information</span></span>|<span data-ttu-id="48d1a-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="48d1a-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="48d1a-131">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-131">Arguments</span></span>

|  <span data-ttu-id="48d1a-132">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-132">Argument</span></span>  | <span data-ttu-id="48d1a-133">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-133">Description</span></span> | <span data-ttu-id="48d1a-134">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="48d1a-135">source-file</span><span class="sxs-lookup"><span data-stu-id="48d1a-135">source-file</span></span> | <span data-ttu-id="48d1a-136">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-136">The source to create a reference from.</span></span> <span data-ttu-id="48d1a-137">OpenAPI 파일이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="48d1a-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="48d1a-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="48d1a-139">URL 추가</span><span class="sxs-lookup"><span data-stu-id="48d1a-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="48d1a-140">옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-140">Options</span></span>

| <span data-ttu-id="48d1a-141">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-141">Short option</span></span>| <span data-ttu-id="48d1a-142">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-142">Long option</span></span>| <span data-ttu-id="48d1a-143">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-143">Description</span></span> | <span data-ttu-id="48d1a-144">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="48d1a-145">-p</span><span class="sxs-lookup"><span data-stu-id="48d1a-145">-p</span></span>|<span data-ttu-id="48d1a-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="48d1a-146">--updateProject</span></span> | <span data-ttu-id="48d1a-147">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-147">The project to operate on.</span></span> |<span data-ttu-id="48d1a-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="48d1a-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="48d1a-149">-o</span><span class="sxs-lookup"><span data-stu-id="48d1a-149">-o</span></span>|<span data-ttu-id="48d1a-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="48d1a-150">--output-file</span></span> | <span data-ttu-id="48d1a-151">OpenAPI 파일의 로컬 복사본을 저장할 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="48d1a-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="48d1a-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="48d1a-153">-c</span><span class="sxs-lookup"><span data-stu-id="48d1a-153">-c</span></span>|<span data-ttu-id="48d1a-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="48d1a-154">--code-generator</span></span>| <span data-ttu-id="48d1a-155">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="48d1a-156">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="48d1a-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="48d1a-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="48d1a-158">-H</span><span class="sxs-lookup"><span data-stu-id="48d1a-158">-h</span></span>|<span data-ttu-id="48d1a-159">--help</span><span class="sxs-lookup"><span data-stu-id="48d1a-159">--help</span></span>|<span data-ttu-id="48d1a-160">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-160">Show help information</span></span>|<span data-ttu-id="48d1a-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="48d1a-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="48d1a-162">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-162">Arguments</span></span>

|  <span data-ttu-id="48d1a-163">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-163">Argument</span></span>  | <span data-ttu-id="48d1a-164">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-164">Description</span></span> | <span data-ttu-id="48d1a-165">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="48d1a-166">원본-URL</span><span class="sxs-lookup"><span data-stu-id="48d1a-166">source-URL</span></span> | <span data-ttu-id="48d1a-167">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-167">The source to create a reference from.</span></span> <span data-ttu-id="48d1a-168">URL이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-168">Must be a URL.</span></span> |<span data-ttu-id="48d1a-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="48d1a-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="48d1a-170">제거</span><span class="sxs-lookup"><span data-stu-id="48d1a-170">Remove</span></span>

<span data-ttu-id="48d1a-171">*.csproj* 파일에서 지정된 파일 이름과 일치하는 OpenAPI 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="48d1a-172">OpenAPI 참조가 제거되면 클라이언트가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="48d1a-173">로컬 *.json* and *.yaml* 파일이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="48d1a-174">옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-174">Options</span></span>

| <span data-ttu-id="48d1a-175">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-175">Short option</span></span>| <span data-ttu-id="48d1a-176">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-176">Long option</span></span>| <span data-ttu-id="48d1a-177">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-177">Description</span></span>| <span data-ttu-id="48d1a-178">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="48d1a-179">-p</span><span class="sxs-lookup"><span data-stu-id="48d1a-179">-p</span></span>|<span data-ttu-id="48d1a-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="48d1a-180">--updateProject</span></span> | <span data-ttu-id="48d1a-181">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-181">The project to operate on.</span></span> |<span data-ttu-id="48d1a-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="48d1a-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="48d1a-183">-H</span><span class="sxs-lookup"><span data-stu-id="48d1a-183">-h</span></span>|<span data-ttu-id="48d1a-184">--help</span><span class="sxs-lookup"><span data-stu-id="48d1a-184">--help</span></span>|<span data-ttu-id="48d1a-185">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-185">Show help information</span></span>|<span data-ttu-id="48d1a-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="48d1a-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="48d1a-187">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-187">Arguments</span></span>

|  <span data-ttu-id="48d1a-188">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-188">Argument</span></span>  | <span data-ttu-id="48d1a-189">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-189">Description</span></span>| <span data-ttu-id="48d1a-190">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="48d1a-191">source-file</span><span class="sxs-lookup"><span data-stu-id="48d1a-191">source-file</span></span> | <span data-ttu-id="48d1a-192">참조를 제거할 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-192">The source to remove the reference to.</span></span> |<span data-ttu-id="48d1a-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="48d1a-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="48d1a-194">새로 고침</span><span class="sxs-lookup"><span data-stu-id="48d1a-194">Refresh</span></span>

<span data-ttu-id="48d1a-195">다운로드 URL의 최신 콘텐츠를 사용하여 다운로드한 파일의 로컬 버전을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="48d1a-196">옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-196">Options</span></span>

| <span data-ttu-id="48d1a-197">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-197">Short option</span></span>| <span data-ttu-id="48d1a-198">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="48d1a-198">Long option</span></span>| <span data-ttu-id="48d1a-199">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-199">Description</span></span> | <span data-ttu-id="48d1a-200">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="48d1a-201">-p</span><span class="sxs-lookup"><span data-stu-id="48d1a-201">-p</span></span>|<span data-ttu-id="48d1a-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="48d1a-202">--updateProject</span></span> | <span data-ttu-id="48d1a-203">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-203">The project to operate on.</span></span> | <span data-ttu-id="48d1a-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="48d1a-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="48d1a-205">-H</span><span class="sxs-lookup"><span data-stu-id="48d1a-205">-h</span></span>|<span data-ttu-id="48d1a-206">--help</span><span class="sxs-lookup"><span data-stu-id="48d1a-206">--help</span></span>|<span data-ttu-id="48d1a-207">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-207">Show help information</span></span>|<span data-ttu-id="48d1a-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="48d1a-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="48d1a-209">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-209">Arguments</span></span>

|  <span data-ttu-id="48d1a-210">인수</span><span class="sxs-lookup"><span data-stu-id="48d1a-210">Argument</span></span>  | <span data-ttu-id="48d1a-211">설명</span><span class="sxs-lookup"><span data-stu-id="48d1a-211">Description</span></span> | <span data-ttu-id="48d1a-212">예제</span><span class="sxs-lookup"><span data-stu-id="48d1a-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="48d1a-213">원본-URL</span><span class="sxs-lookup"><span data-stu-id="48d1a-213">source-URL</span></span> | <span data-ttu-id="48d1a-214">참조를 새로 고칠 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="48d1a-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="48d1a-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="48d1a-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
