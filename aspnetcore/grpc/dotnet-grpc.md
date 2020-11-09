---
title: dotnet-grpc를 사용하여 Protobuf 참조를 관리하세요.
author: juntaoluo
description: dotnet-grpc 전역 도구를 사용하여 Protobuf 참조를 추가, 업데이트, 제거 및 나열하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059912"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="62cfd-103">dotnet-grpc를 사용하여 Protobuf 참조를 관리하세요.</span><span class="sxs-lookup"><span data-stu-id="62cfd-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="62cfd-104">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="62cfd-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="62cfd-105">`dotnet-grpc`는 .NET gRPC 프로젝트 내에서 [Protobuf( *.proto* )](xref:grpc/basics#proto-file) 참조를 관리하기 위한 .NET Core 전역 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf ( *.proto* )](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="62cfd-106">이 도구를 사용하여 Protobuf 참조를 추가, 새로 고침, 제거 및 나열할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="62cfd-107">설치</span><span class="sxs-lookup"><span data-stu-id="62cfd-107">Installation</span></span>

<span data-ttu-id="62cfd-108">`dotnet-grpc` [.NET Core 전역 도구](/dotnet/core/tools/global-tools)를 설치하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="62cfd-109">참조 추가</span><span class="sxs-lookup"><span data-stu-id="62cfd-109">Add references</span></span>

<span data-ttu-id="62cfd-110">`dotnet-grpc`를 사용하여 *.csproj* 파일에 Protobuf 참조를 `<Protobuf />` 항목으로 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="62cfd-111">Protobuf 참조는 C# 클라이언트 및/또는 서버 자산을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="62cfd-112">`dotnet-grpc` 도구는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="62cfd-113">디스크의 로컬 파일에서 Protobuf 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="62cfd-114">URL로 지정된 원격 파일에서 Protobuf 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="62cfd-115">올바른 gRPC 패키지 종속성이 프로젝트에 추가되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="62cfd-116">예를 들어 웹앱에는 `Grpc.AspNetCore` 패키지가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="62cfd-117">`Grpc.AspNetCore`는 gRPC 서버 및 클라이언트 라이브러리와 도구 지원을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="62cfd-118">또는 콘솔 앱에는 gRPC 클라이언트 라이브러리와 도구 지원만 포함하는 `Grpc.Net.Client`, `Grpc.Tools` 및 `Google.Protobuf` 패키지가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="62cfd-119">파일 추가</span><span class="sxs-lookup"><span data-stu-id="62cfd-119">Add file</span></span>

<span data-ttu-id="62cfd-120">`add-file` 명령은 디스크의 로컬 파일을 Protobuf 참조로 추가하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="62cfd-121">제공되는 파일 경로와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-121">The file paths provided:</span></span>

* <span data-ttu-id="62cfd-122">현재 디렉터리 기준의 상대 경로이거나 절대 경로일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="62cfd-123">패턴 기반 파일 [와일드카드 사용](https://wikipedia.org/wiki/Glob_(programming))을 위해 와일드카드를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="62cfd-124">프로젝트 디렉터리 외부에 파일이 있는 경우, Visual Studio의 `Protos` 폴더 아래에 있는 파일을 표시하기 위해 `Link` 요소가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="62cfd-125">사용법</span><span class="sxs-lookup"><span data-stu-id="62cfd-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="62cfd-126">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-126">Arguments</span></span>

| <span data-ttu-id="62cfd-127">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-127">Argument</span></span> | <span data-ttu-id="62cfd-128">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-128">Description</span></span> |
|-|-|
| <span data-ttu-id="62cfd-129">파일</span><span class="sxs-lookup"><span data-stu-id="62cfd-129">files</span></span> | <span data-ttu-id="62cfd-130">protobuf 파일 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-130">The protobuf file references.</span></span> <span data-ttu-id="62cfd-131">로컬 protobuf 파일의 GLOB 경로일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="62cfd-132">옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-132">Options</span></span>

| <span data-ttu-id="62cfd-133">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-133">Short option</span></span> | <span data-ttu-id="62cfd-134">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-134">Long option</span></span> | <span data-ttu-id="62cfd-135">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="62cfd-136">-p</span><span class="sxs-lookup"><span data-stu-id="62cfd-136">-p</span></span> | <span data-ttu-id="62cfd-137">--project</span><span class="sxs-lookup"><span data-stu-id="62cfd-137">--project</span></span> | <span data-ttu-id="62cfd-138">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-138">The path to the project file to operate on.</span></span> <span data-ttu-id="62cfd-139">파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="62cfd-140">-S</span><span class="sxs-lookup"><span data-stu-id="62cfd-140">-s</span></span> | <span data-ttu-id="62cfd-141">--services</span><span class="sxs-lookup"><span data-stu-id="62cfd-141">--services</span></span> | <span data-ttu-id="62cfd-142">생성해야 하는 gRPC 서비스의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="62cfd-143">`Default`를 지정한 경우, 웹 프로젝트에는 `Both`가 사용되고 웹 프로젝트가 아닌 프로젝트에는 `Client`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="62cfd-144">허용되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="62cfd-145">-i</span><span class="sxs-lookup"><span data-stu-id="62cfd-145">-i</span></span> | <span data-ttu-id="62cfd-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="62cfd-146">--additional-import-dirs</span></span> | <span data-ttu-id="62cfd-147">protobuf 파일의 가져오기를 확인할 때 사용할 추가 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="62cfd-148">세미콜론으로 구분된 경로 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="62cfd-149">--access</span><span class="sxs-lookup"><span data-stu-id="62cfd-149">--access</span></span> | <span data-ttu-id="62cfd-150">생성된 C# 클래스에 사용할 액세스 한정자입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="62cfd-151">기본값은 `Public`입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-151">The default value is `Public`.</span></span> <span data-ttu-id="62cfd-152">허용되는 값은 `Internal` 및 `Public`입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="62cfd-153">URL 추가</span><span class="sxs-lookup"><span data-stu-id="62cfd-153">Add URL</span></span>

<span data-ttu-id="62cfd-154">`add-url` 명령은 소스 URL에 지정된 원격 파일을 Protobuf 참조로 추가하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="62cfd-155">원격 파일을 다운로드할 위치를 지정하려면 파일 경로를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="62cfd-156">파일 경로는 현재 디렉터리 기준의 상대 경로이거나 절대 경로일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="62cfd-157">파일 경로가 프로젝트 디렉터리 외부에 있는 경우, Visual Studio의 `Protos` 가상 폴더 아래에 있는 파일을 표시하기 위해 `Link` 요소가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="62cfd-158">사용법</span><span class="sxs-lookup"><span data-stu-id="62cfd-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="62cfd-159">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-159">Arguments</span></span>

| <span data-ttu-id="62cfd-160">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-160">Argument</span></span> | <span data-ttu-id="62cfd-161">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-161">Description</span></span> |
|-|-|
| <span data-ttu-id="62cfd-162">url</span><span class="sxs-lookup"><span data-stu-id="62cfd-162">url</span></span> | <span data-ttu-id="62cfd-163">원격 protobuf 파일의 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="62cfd-164">옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-164">Options</span></span>

| <span data-ttu-id="62cfd-165">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-165">Short option</span></span> | <span data-ttu-id="62cfd-166">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-166">Long option</span></span> | <span data-ttu-id="62cfd-167">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="62cfd-168">-o</span><span class="sxs-lookup"><span data-stu-id="62cfd-168">-o</span></span> | <span data-ttu-id="62cfd-169">--output</span><span class="sxs-lookup"><span data-stu-id="62cfd-169">--output</span></span> | <span data-ttu-id="62cfd-170">원격 protobuf 파일의 다운로드 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="62cfd-171">필수 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-171">This is a required option.</span></span>
| <span data-ttu-id="62cfd-172">-p</span><span class="sxs-lookup"><span data-stu-id="62cfd-172">-p</span></span> | <span data-ttu-id="62cfd-173">--project</span><span class="sxs-lookup"><span data-stu-id="62cfd-173">--project</span></span> | <span data-ttu-id="62cfd-174">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-174">The path to the project file to operate on.</span></span> <span data-ttu-id="62cfd-175">파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="62cfd-176">-S</span><span class="sxs-lookup"><span data-stu-id="62cfd-176">-s</span></span> | <span data-ttu-id="62cfd-177">--services</span><span class="sxs-lookup"><span data-stu-id="62cfd-177">--services</span></span> | <span data-ttu-id="62cfd-178">생성해야 하는 gRPC 서비스의 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="62cfd-179">`Default`를 지정한 경우, 웹 프로젝트에는 `Both`가 사용되고 웹 프로젝트가 아닌 프로젝트에는 `Client`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="62cfd-180">허용되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="62cfd-181">-i</span><span class="sxs-lookup"><span data-stu-id="62cfd-181">-i</span></span> | <span data-ttu-id="62cfd-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="62cfd-182">--additional-import-dirs</span></span> | <span data-ttu-id="62cfd-183">protobuf 파일의 가져오기를 확인할 때 사용할 추가 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="62cfd-184">세미콜론으로 구분된 경로 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="62cfd-185">--access</span><span class="sxs-lookup"><span data-stu-id="62cfd-185">--access</span></span> | <span data-ttu-id="62cfd-186">생성된 C# 클래스에 사용할 액세스 한정자입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="62cfd-187">기본값은 `Public`여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-187">Default value is `Public`.</span></span> <span data-ttu-id="62cfd-188">허용되는 값은 `Internal` 및 `Public`입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="62cfd-189">제거</span><span class="sxs-lookup"><span data-stu-id="62cfd-189">Remove</span></span>

<span data-ttu-id="62cfd-190">`remove` 명령은 *.csproj* 파일에서 Protobuf 참조를 제거하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="62cfd-191">이 명령은 경로 인수와 소스 URL을 인수로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="62cfd-192">도구와 관련해서 다음 사항을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-192">The tool:</span></span>

* <span data-ttu-id="62cfd-193">Protobuf 참조만 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="62cfd-194">원래 원격 URL에서 다운로드된 경우에도 *.proto* 파일을 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="62cfd-195">사용법</span><span class="sxs-lookup"><span data-stu-id="62cfd-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="62cfd-196">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-196">Arguments</span></span>

| <span data-ttu-id="62cfd-197">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-197">Argument</span></span> | <span data-ttu-id="62cfd-198">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-198">Description</span></span> |
|-|-|
| <span data-ttu-id="62cfd-199">참조</span><span class="sxs-lookup"><span data-stu-id="62cfd-199">references</span></span> | <span data-ttu-id="62cfd-200">제거할 protobuf 참조의 URL 또는 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="62cfd-201">옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-201">Options</span></span>

| <span data-ttu-id="62cfd-202">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-202">Short option</span></span> | <span data-ttu-id="62cfd-203">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-203">Long option</span></span> | <span data-ttu-id="62cfd-204">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="62cfd-205">-p</span><span class="sxs-lookup"><span data-stu-id="62cfd-205">-p</span></span> | <span data-ttu-id="62cfd-206">--project</span><span class="sxs-lookup"><span data-stu-id="62cfd-206">--project</span></span> | <span data-ttu-id="62cfd-207">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-207">The path to the project file to operate on.</span></span> <span data-ttu-id="62cfd-208">파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="62cfd-209">새로 고침</span><span class="sxs-lookup"><span data-stu-id="62cfd-209">Refresh</span></span>

<span data-ttu-id="62cfd-210">`refresh` 명령은 소스 URL의 최신 콘텐츠로 원격 참조를 업데이트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="62cfd-211">다운로드 파일 경로와 소스 URL을 모두 사용하여 업데이트할 참조를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="62cfd-212">참고:</span><span class="sxs-lookup"><span data-stu-id="62cfd-212">Note:</span></span>

* <span data-ttu-id="62cfd-213">파일 콘텐츠의 해시를 비교하여 로컬 파일을 업데이트해야 하는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="62cfd-214">타임스탬프 정보는 비교되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-214">No timestamp information is compared.</span></span>

<span data-ttu-id="62cfd-215">업데이트가 필요한 경우, 도구는 항상 로컬 파일을 원격 파일로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="62cfd-216">사용법</span><span class="sxs-lookup"><span data-stu-id="62cfd-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="62cfd-217">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-217">Arguments</span></span>

| <span data-ttu-id="62cfd-218">인수</span><span class="sxs-lookup"><span data-stu-id="62cfd-218">Argument</span></span> | <span data-ttu-id="62cfd-219">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-219">Description</span></span> |
|-|-|
| <span data-ttu-id="62cfd-220">참조</span><span class="sxs-lookup"><span data-stu-id="62cfd-220">references</span></span> | <span data-ttu-id="62cfd-221">업데이트해야 하는 원격 protobuf 참조의 URL 또는 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="62cfd-222">모든 원격 참조를 새로 고치려면 이 인수를 비워 둡니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="62cfd-223">옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-223">Options</span></span>

| <span data-ttu-id="62cfd-224">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-224">Short option</span></span> | <span data-ttu-id="62cfd-225">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-225">Long option</span></span> | <span data-ttu-id="62cfd-226">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="62cfd-227">-p</span><span class="sxs-lookup"><span data-stu-id="62cfd-227">-p</span></span> | <span data-ttu-id="62cfd-228">--project</span><span class="sxs-lookup"><span data-stu-id="62cfd-228">--project</span></span> | <span data-ttu-id="62cfd-229">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-229">The path to the project file to operate on.</span></span> <span data-ttu-id="62cfd-230">파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="62cfd-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="62cfd-231">--dry-run</span></span> | <span data-ttu-id="62cfd-232">새 콘텐츠를 다운로드하지 않고 업데이트되는 파일 목록을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="62cfd-233">목록</span><span class="sxs-lookup"><span data-stu-id="62cfd-233">List</span></span>

<span data-ttu-id="62cfd-234">`list` 명령은 프로젝트 파일에 모든 Protobuf 참조를 표시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="62cfd-235">열의 모든 값이 기본값인 경우 열을 생략할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="62cfd-236">사용법</span><span class="sxs-lookup"><span data-stu-id="62cfd-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="62cfd-237">옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-237">Options</span></span>

| <span data-ttu-id="62cfd-238">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-238">Short option</span></span> | <span data-ttu-id="62cfd-239">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="62cfd-239">Long option</span></span> | <span data-ttu-id="62cfd-240">설명</span><span class="sxs-lookup"><span data-stu-id="62cfd-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="62cfd-241">-p</span><span class="sxs-lookup"><span data-stu-id="62cfd-241">-p</span></span> | <span data-ttu-id="62cfd-242">--project</span><span class="sxs-lookup"><span data-stu-id="62cfd-242">--project</span></span> | <span data-ttu-id="62cfd-243">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-243">The path to the project file to operate on.</span></span> <span data-ttu-id="62cfd-244">파일을 지정하지 않은 경우, 명령은 현재 디렉터리에서 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="62cfd-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62cfd-245">추가 자료</span><span class="sxs-lookup"><span data-stu-id="62cfd-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
