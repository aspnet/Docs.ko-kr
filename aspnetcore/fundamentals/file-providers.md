---
title: ASP.NET Core의 파일 공급자
author: rick-anderson
description: ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 16e5ead9898125c804da4d60322510474201d897
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059444"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="7bb06-103">ASP.NET Core의 파일 공급자</span><span class="sxs-lookup"><span data-stu-id="7bb06-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="7bb06-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7bb06-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7bb06-105">ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="7bb06-106">파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="7bb06-107">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="7bb06-107">For example:</span></span>

* <span data-ttu-id="7bb06-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>는 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)와 [웹 루트](xref:fundamentals/index#web-root)를 `IFileProvider` 형식으로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="7bb06-109">[정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="7bb06-110">[Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="7bb06-111">.NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="7bb06-112">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7bb06-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="7bb06-113">파일 공급자 인터페이스</span><span class="sxs-lookup"><span data-stu-id="7bb06-113">File Provider interfaces</span></span>

<span data-ttu-id="7bb06-114">기본 인터페이스는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="7bb06-115">`IFileProvider`는 다음을 수행하는 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="7bb06-116">파일 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="7bb06-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="7bb06-117">디렉터리 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="7bb06-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="7bb06-118">변경 알림을 설정합니다(<xref:Microsoft.Extensions.Primitives.IChangeToken> 사용).</span><span class="sxs-lookup"><span data-stu-id="7bb06-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="7bb06-119">`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="7bb06-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(바이트)</span><span class="sxs-lookup"><span data-stu-id="7bb06-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="7bb06-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 날짜</span><span class="sxs-lookup"><span data-stu-id="7bb06-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="7bb06-122"><xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> 메서드를 사용해서 파일을 읽을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="7bb06-123">*FileProviderSample* 샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에서 파일 공급자를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="7bb06-124">파일 공급자 구현</span><span class="sxs-lookup"><span data-stu-id="7bb06-124">File Provider implementations</span></span>

<span data-ttu-id="7bb06-125">다음 표에서는 `IFileProvider`의 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="7bb06-126">구현</span><span class="sxs-lookup"><span data-stu-id="7bb06-126">Implementation</span></span> | <span data-ttu-id="7bb06-127">설명</span><span class="sxs-lookup"><span data-stu-id="7bb06-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="7bb06-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="7bb06-129">하나 이상의 다른 공급자의 파일 및 디렉터리에 대한 결합된 액세스를 제공하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="7bb06-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="7bb06-131">어셈블리에 포함된 파일에 액세스하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="7bb06-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="7bb06-133">시스템의 물리적 파일에 액세스하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="7bb06-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-134">PhysicalFileProvider</span></span>

<span data-ttu-id="7bb06-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 는 실제 파일 시스템에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="7bb06-136">`PhysicalFileProvider`는 <xref:System.IO.File?displayProperty=fullName> 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="7bb06-137">이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="7bb06-138">가장 공통적인 `PhysicalFileProvider` 생성 및 사용 시나리오는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 생성자 내에 `IFileProvider`를 요청하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7bb06-139">이 공급자를 직접 인스턴스화하는 경우 절대 디렉터리 경로가 필요합니다. 이 경로는 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="7bb06-140">디렉터리 경로에서는 GLOB 패턴이 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="7bb06-141">다음 코드는 `PhysicalFileProvider`를 사용하여 디렉터리 콘텐츠와 파일 정보를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="7bb06-142">이전 예제의 형식:</span><span class="sxs-lookup"><span data-stu-id="7bb06-142">Types in the preceding example:</span></span>

* <span data-ttu-id="7bb06-143">`provider`는 `IFileProvider`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="7bb06-144">`contents`는 `IDirectoryContents`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="7bb06-145">`fileInfo`는 `IFileInfo`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="7bb06-146">파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="7bb06-147">GLOB 패턴은 `GetFileInfo` 메서드에 전달할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="7bb06-148">파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="7bb06-149">*FileProviderSample* 샘플 앱은 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>를 사용하여 `Startup.ConfigureServices` 메서드에 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="7bb06-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="7bb06-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>는 어셈블리에 포함된 파일에 접근하기 위한 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="7bb06-152">`ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="7bb06-153">포함된 파일의 매니페스트를 생성하려면</span><span class="sxs-lookup"><span data-stu-id="7bb06-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="7bb06-154">프로젝트에 [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="7bb06-155">`<GenerateEmbeddedFilesManifest>` 속성을 `true`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="7bb06-156">[\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)에 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="7bb06-157">[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="7bb06-158">*FileProviderSample* 샘플 앱은 `ManifestEmbeddedFileProvider`를 만들고 현재 실행 중인 어셈블리를 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="7bb06-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7bb06-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="7bb06-160">추가 오버로드를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="7bb06-161">상대 파일 경로 지정</span><span class="sxs-lookup"><span data-stu-id="7bb06-161">Specify a relative file path.</span></span>
* <span data-ttu-id="7bb06-162">마지막으로 수정한 날짜로 파일의 범위 지정</span><span class="sxs-lookup"><span data-stu-id="7bb06-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="7bb06-163">포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정</span><span class="sxs-lookup"><span data-stu-id="7bb06-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="7bb06-164">오버로드</span><span class="sxs-lookup"><span data-stu-id="7bb06-164">Overload</span></span> | <span data-ttu-id="7bb06-165">설명</span><span class="sxs-lookup"><span data-stu-id="7bb06-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="7bb06-166">선택적인 `root` 상대 경로 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="7bb06-167">`root`를 지정하여 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*>에 대한 호출의 범위를 제공된 경로의 해당 리소스로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="7bb06-168">선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜(<xref:System.DateTimeOffset>) 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="7bb06-169">`lastModified` 날짜는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>가 반환한 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="7bb06-170">선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="7bb06-171">`manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="7bb06-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-172">CompositeFileProvider</span></span>

<span data-ttu-id="7bb06-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 는 `IFileProvider` 의 인스턴스들을 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="7bb06-174">`CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="7bb06-175">*FileProviderSample* 샘플 앱에서 `PhysicalFileProvider`와 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="7bb06-176">프로젝트의 `Startup.ConfigureServices` 메서드에는 다음과 같은 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="7bb06-177">변경 내용 관찰</span><span class="sxs-lookup"><span data-stu-id="7bb06-177">Watch for changes</span></span>

<span data-ttu-id="7bb06-178"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> 메서드는 하나 이상의 파일 또는 디렉터리의 변경 사항을 살펴보는 시나리오를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="7bb06-179">`Watch` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-179">The `Watch` method:</span></span>

* <span data-ttu-id="7bb06-180">파일 경로 문자열을 받습니다. 이 문자열은 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="7bb06-181"><xref:Microsoft.Extensions.Primitives.IChangeToken>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="7bb06-182">결과 변경 토큰은 다음을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="7bb06-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="7bb06-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="7bb06-185">각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="7bb06-186">모니터링을 지속적으로 수행하기 위해서는 다음 예처럼 <xref:System.Threading.Tasks.TaskCompletionSource`1>를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="7bb06-187">*WatchConsole* 샘플 앱은 *TextFiles* 디렉터리의 *.txt* 파일이 수정될 때마다 메시지를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="7bb06-188">Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="7bb06-189">`DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="7bb06-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="7bb06-190">GLOB 패턴</span><span class="sxs-lookup"><span data-stu-id="7bb06-190">Glob patterns</span></span>

<span data-ttu-id="7bb06-191">파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴* 이라고 부르는 와일드카드 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="7bb06-192">이러한 패턴을 사용하여 파일 그룹을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="7bb06-193">두 개의 와일드카드 문자는 `*`와 `**`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="7bb06-194">현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="7bb06-195">파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="7bb06-196">여러 디렉터리 수준에서 모든 것을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="7bb06-197">디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="7bb06-198">다음 표에서는 GLOB 패턴의 일반적인 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="7bb06-199">무늬</span><span class="sxs-lookup"><span data-stu-id="7bb06-199">Pattern</span></span>  |<span data-ttu-id="7bb06-200">설명</span><span class="sxs-lookup"><span data-stu-id="7bb06-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="7bb06-201">특정 디렉터리에 있는 특정 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="7bb06-202">특정 디렉터리에서 확장명이 *.txt* 인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="7bb06-203">*directory* 폴더보다 정확히 한 수준 아래의 디렉터리에서 모든 *appsettings.json* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="7bb06-204">*directory* 폴더 아래의 모든 곳에서 찾은 확장명이 *.txt* 인 모든 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7bb06-205">ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="7bb06-206">파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="7bb06-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)와 [웹 루트](xref:fundamentals/index#web-root)를 `IFileProvider` 형식으로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="7bb06-208">[정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="7bb06-209">[Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="7bb06-210">.NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="7bb06-211">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7bb06-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="7bb06-212">파일 공급자 인터페이스</span><span class="sxs-lookup"><span data-stu-id="7bb06-212">File Provider interfaces</span></span>

<span data-ttu-id="7bb06-213">기본 인터페이스는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="7bb06-214">`IFileProvider`는 다음을 수행하는 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="7bb06-215">파일 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="7bb06-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="7bb06-216">디렉터리 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="7bb06-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="7bb06-217">변경 알림을 설정합니다(<xref:Microsoft.Extensions.Primitives.IChangeToken> 사용).</span><span class="sxs-lookup"><span data-stu-id="7bb06-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="7bb06-218">`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="7bb06-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(바이트)</span><span class="sxs-lookup"><span data-stu-id="7bb06-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="7bb06-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 날짜</span><span class="sxs-lookup"><span data-stu-id="7bb06-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="7bb06-221">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 메서드를 사용하여 파일에서 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="7bb06-222">샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에 파일 공급자를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="7bb06-223">파일 공급자 구현</span><span class="sxs-lookup"><span data-stu-id="7bb06-223">File Provider implementations</span></span>

<span data-ttu-id="7bb06-224">`IFileProvider`의 세 가지 구현을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="7bb06-225">구현</span><span class="sxs-lookup"><span data-stu-id="7bb06-225">Implementation</span></span> | <span data-ttu-id="7bb06-226">설명</span><span class="sxs-lookup"><span data-stu-id="7bb06-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="7bb06-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="7bb06-228">물리적 공급자는 시스템의 물리적 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="7bb06-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="7bb06-230">매니페스트 임베디드 공급자는 어셈블리에 포함된 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="7bb06-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="7bb06-232">마지막으로 복합 공급자는 하나 이상의 개별 공급자로부터 얻어진 파일 및 디렉터리에 대한 복합적인 접근을 지원하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="7bb06-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-233">PhysicalFileProvider</span></span>

<span data-ttu-id="7bb06-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 는 실제 파일 시스템에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="7bb06-235">`PhysicalFileProvider`는 <xref:System.IO.File?displayProperty=fullName> 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="7bb06-236">이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="7bb06-237">가장 공통적인 `PhysicalFileProvider` 생성 및 사용 시나리오는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 생성자 내에 `IFileProvider`를 요청하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7bb06-238">이 공급자를 직접 인스턴스화하는 경우 디렉터리 경로가 필요하며, 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="7bb06-239">다음 코드는 `PhysicalFileProvider`를 생성하고 이를 사용하여 디렉터리 콘텐츠 및 파일 정보를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="7bb06-240">이전 예제의 형식:</span><span class="sxs-lookup"><span data-stu-id="7bb06-240">Types in the preceding example:</span></span>

* <span data-ttu-id="7bb06-241">`provider`는 `IFileProvider`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="7bb06-242">`contents`는 `IDirectoryContents`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="7bb06-243">`fileInfo`는 `IFileInfo`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="7bb06-244">파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="7bb06-245">파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="7bb06-246">샘플 앱은 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)를 사용하여 앱의 `Startup.ConfigureServices` 클래스에 공급자를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="7bb06-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="7bb06-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>는 어셈블리에 포함된 파일에 접근하기 위한 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="7bb06-249">`ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="7bb06-250">포함된 파일의 매니페스트를 생성하려면 `<GenerateEmbeddedFilesManifest>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="7bb06-251">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)를 사용하여 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="7bb06-252">[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="7bb06-253">샘플 앱은 `ManifestEmbeddedFileProvider`를 생성하고 현재 실행 중인 어셈블리를 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="7bb06-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7bb06-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="7bb06-255">추가 오버로드를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="7bb06-256">상대 파일 경로 지정</span><span class="sxs-lookup"><span data-stu-id="7bb06-256">Specify a relative file path.</span></span>
* <span data-ttu-id="7bb06-257">마지막으로 수정한 날짜로 파일의 범위 지정</span><span class="sxs-lookup"><span data-stu-id="7bb06-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="7bb06-258">포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정</span><span class="sxs-lookup"><span data-stu-id="7bb06-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="7bb06-259">오버로드</span><span class="sxs-lookup"><span data-stu-id="7bb06-259">Overload</span></span> | <span data-ttu-id="7bb06-260">설명</span><span class="sxs-lookup"><span data-stu-id="7bb06-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="7bb06-261">선택적인 `root` 상대 경로 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="7bb06-262">`root`를 지정하여 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*>에 대한 호출의 범위를 제공된 경로의 해당 리소스로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="7bb06-263">선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜(<xref:System.DateTimeOffset>) 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="7bb06-264">`lastModified` 날짜는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>가 반환한 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="7bb06-265">선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="7bb06-266">`manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="7bb06-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7bb06-267">CompositeFileProvider</span></span>

<span data-ttu-id="7bb06-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 는 `IFileProvider` 의 인스턴스들을 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="7bb06-269">`CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="7bb06-270">샘플 앱에서 `PhysicalFileProvider` 및 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="7bb06-271">변경 내용 관찰</span><span class="sxs-lookup"><span data-stu-id="7bb06-271">Watch for changes</span></span>

<span data-ttu-id="7bb06-272">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 메서드는 하나 이상의 파일 또는 디렉터리의 변경 내용을 관찰하는 시나리오를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="7bb06-273">`Watch`는 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있는 경로 문자열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="7bb06-274">`Watch`는 <xref:Microsoft.Extensions.Primitives.IChangeToken>을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="7bb06-275">변경 토큰은 다음을 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-275">The change token exposes:</span></span>

* <span data-ttu-id="7bb06-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="7bb06-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="7bb06-278">각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="7bb06-279">모니터링을 지속적으로 수행하기 위해서는 다음 예처럼 <xref:System.Threading.Tasks.TaskCompletionSource`1>를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="7bb06-280">샘플 앱에서 *WatchConsole* 콘솔 앱은 텍스트 파일이 수정될 때마다 메시지를 표시하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="7bb06-281">Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="7bb06-282">`DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="7bb06-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="7bb06-283">GLOB 패턴</span><span class="sxs-lookup"><span data-stu-id="7bb06-283">Glob patterns</span></span>

<span data-ttu-id="7bb06-284">파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴* 이라고 부르는 와일드카드 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="7bb06-285">이러한 패턴을 사용하여 파일 그룹을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="7bb06-286">두 개의 와일드카드 문자는 `*`와 `**`입니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="7bb06-287">현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="7bb06-288">파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="7bb06-289">여러 디렉터리 수준에서 모든 것을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="7bb06-290">디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="7bb06-291">**GLOB 패턴 예제**</span><span class="sxs-lookup"><span data-stu-id="7bb06-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="7bb06-292">특정 디렉터리에 있는 특정 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="7bb06-293">특정 디렉터리에서 확장명이 *.txt* 인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="7bb06-294">*directory* 폴더보다 정확히 한 수준 아래의 디렉터리에서 모든 `appsettings.json` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="7bb06-295">*directory* 폴더 아래의 모든 곳에서 찾은 확장명이 *.txt* 인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="7bb06-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
