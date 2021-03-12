---
title: ASP.NET Core에서 호스팅 시작 어셈블리 사용
author: rick-anderson
description: IHostingStartup 구현을 사용하여 외부 어셈블리에서 ASP.NET Core 앱을 강화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: b39215a70f990afeb7d3fe0a62981113b154354e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588245"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="e244c-103">ASP.NET Core에서 호스팅 시작 어셈블리 사용</span><span class="sxs-lookup"><span data-stu-id="e244c-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="e244c-104">작성자: [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="e244c-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e244c-105"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(호스팅 시작) 구현에서는 외부 어셈블리에서 시작할 때 앱에 향상된 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e244c-106">예를 들어 외부 라이브러리는 호스팅 시작 구현을 사용하여 앱에 추가 구성 공급자 또는 서비스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e244c-107">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e244c-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e244c-108">HostingStartup 특성</span><span class="sxs-lookup"><span data-stu-id="e244c-108">HostingStartup attribute</span></span>

<span data-ttu-id="e244c-109">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 런타임에 활성화할 호스팅 시작 어셈블리의 현재 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e244c-110">항목 어셈블리 또는 `Startup` 클래스가 포함된 어셈블리에서는 `HostingStartup` 특성을 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-111">`HostingStartup` 특성을 검색할 어셈블리 목록은 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)의 구성에서 런타임에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e244c-112">검색에서 제외할 어셈블리 목록은 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="e244c-113">다음 예제에서는 호스팅 시작 어셈블리의 네임스페이스가 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e244c-114">호스팅 시작 코드를 포함하는 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e244c-115">`HostingStartup` 특성은 일반적으로 호스팅 시작 어셈블리의 `IHostingStartup` 구현 클래스 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e244c-116">로드된 호스팅 시작 어셈블리 검색</span><span class="sxs-lookup"><span data-stu-id="e244c-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e244c-117">로드된 호스팅 시작 어셈블리를 검색하려면 로깅을 사용하도록 설정하고 앱의 로그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e244c-118">어셈블리를 로드할 때 발생하는 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e244c-119">로드된 호스팅 시작 어셈블리는 디버그 수준에서 기록되고 모든 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e244c-120">호스팅 시작 어셈블리의 자동 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="e244c-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e244c-121">호스팅 시작 어셈블리의 자동 로딩을 사용하지 않으려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e244c-122">모든 호스팅 시작 어셈블리가 로드되지 않도록 하려면 다음 중 하나를 `true` 또는 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="e244c-123">호스팅 시작 방지 호스트 구성 설정:</span><span class="sxs-lookup"><span data-stu-id="e244c-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="e244c-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="e244c-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="e244c-125">특정 호스팅 시작 어셈블리가 로드되지 않도록 하려면 시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열로 다음 중 하나를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="e244c-126">호스팅 시작 어셈블리 제외 호스트 구성 설정:</span><span class="sxs-lookup"><span data-stu-id="e244c-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="e244c-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="e244c-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e244c-128">호스트 구성 설정과 환경 변수가 모두 설정되면 호스트 설정이 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e244c-129">호스트 설정 또는 환경 변수를 사용하여 호스팅 시작 어셈블리를 사용하지 않도록 설정하면 어셈블리가 전역적으로 해제되고 앱의 여러 특징을 해제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e244c-130">프로젝트</span><span class="sxs-lookup"><span data-stu-id="e244c-130">Project</span></span>

<span data-ttu-id="e244c-131">다음 프로젝트 형식 중 하나를 사용하여 호스팅 시작을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e244c-132">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e244c-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="e244c-133">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="e244c-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e244c-134">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e244c-134">Class library</span></span>

<span data-ttu-id="e244c-135">클래스 라이브러리에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e244c-136">라이브러리에는 `HostingStartup` 특성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e244c-137">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 Razor 페이지 앱, *HostingStartupApp* 및 클래스 라이브러리, *HostingStartupLibrary* 가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e244c-138">클래스 라이브러리:</span><span class="sxs-lookup"><span data-stu-id="e244c-138">The class library:</span></span>

* <span data-ttu-id="e244c-139">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e244c-140">`ServiceKeyInjection`은 메모리 내 구성 공급자([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*))를 사용하여 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e244c-141">호스팅 시작의 네임스페이스 및 클래스를 식별하는 `HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e244c-142">`ServiceKeyInjection` 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e244c-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e244c-144">앱의 인덱스 페이지는 클래스 라이브러리의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e244c-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e244c-146">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 별도의 호스팅 시작인 *HostingStartupPackage* 를 제공하는 NuGet 패키지 프로젝트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e244c-147">패키지는 앞에서 설명한 클래스 라이브러리와 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e244c-148">패키지:</span><span class="sxs-lookup"><span data-stu-id="e244c-148">The package:</span></span>

* <span data-ttu-id="e244c-149">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e244c-150">`ServiceKeyInjection`은 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e244c-151">`HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e244c-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e244c-153">앱의 인덱스 페이지는 패키지의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e244c-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e244c-155">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="e244c-155">Console app without an entry point</span></span>

<span data-ttu-id="e244c-156">*이 방법은 .NET Framework가 아닌 .NET Core 앱에서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e244c-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e244c-157">활성화에 대한 컴파일 시간 참조가 필요하지 않은 동적 호스팅 시작 기능 향상은 `HostingStartup` 특성이 포함되는 진입점 없는 콘솔 앱에서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-158">콘솔 앱을 게시하면 런타임 저장소에서 사용할 수 있는 호스팅 시작 어셈블리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e244c-159">다음과 같은 이유로 이 프로세스에서는 진입점 없는 콘솔 앱이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e244c-160">호스팅 시작 어셈블리에서 호스팅 시작을 사용하려면 종속성 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e244c-161">종속성 파일은 라이브러리가 아닌 앱을 게시하여 생성된 실행 가능한 앱 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e244c-162">라이브러리를 [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)에 직접 추가할 수 없습니다. 이 저장소에는 공유 런타임을 대상으로 하는 실행 가능한 프로젝트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e244c-163">동적 호스팅 시작 만들기:</span><span class="sxs-lookup"><span data-stu-id="e244c-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e244c-164">호스팅 시작 어셈블리는 다음과 같은 진입점 없는 콘솔 앱에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e244c-165">`IHostingStartup` 구현이 포함되는 클래스를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e244c-166">`IHostingStartup` 구현 클래스를 식별하는 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e244c-167">콘솔 앱은 호스팅 시작의 종속성을 얻기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e244c-168">콘솔 앱을 게시하면 사용되지 않는 종속성이 종속성 파일에서 트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e244c-169">종속성 파일은 호스팅 시작 어셈블리의 런타임 위치를 설정하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e244c-170">호스팅 시작 어셈블리 및 해당 종속성 파일은 런타임 패키지 저장소에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e244c-171">호스팅 시작 어셈블리 및 해당 종속성 파일을 검색하기 위해 한 쌍의 환경 변수에 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e244c-172">콘솔 앱은 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="e244c-173">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 <xref:Microsoft.AspNetCore.Hosting.IWebHost>를 빌드할 때 로드 및 실행을 위해 `IHostingStartup`의 구현으로 클래스를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e244c-174">다음 예제에서 네임스페이스는 `StartupEnhancement`이고 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e244c-175">클래스는 `IHostingStartup`을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e244c-176">클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e244c-177">호스팅 시작 어셈블리의 `IHostingStartup.Configure`는 사용자 코드로 `Startup.Configure` 전에 런타임에서 호출됩니다. 그러면 사용자 코드가 호스팅 시작 어셈블리에서 제공하는 모든 구성을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e244c-178">`IHostingStartup` 프로젝트를 빌드할 때 종속성 파일( *.deps.json*)은 어셈블리의 `runtime` 위치를 *bin* 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e244c-179">파일의 일부만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-179">Only part of the file is shown.</span></span> <span data-ttu-id="e244c-180">이 예제의 어셈블리 이름은 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e244c-181">호스팅 시작으로 제공하는 구성</span><span class="sxs-lookup"><span data-stu-id="e244c-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e244c-182">호스팅 시작의 구성을 우선할지, 앱의 구성을 우선할지에 따라 두 가지 방법으로 구성을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e244c-183">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행된 후 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e244c-184">이 방법을 사용하면 호스팅 시작 구성이 앱의 구성보다 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e244c-185">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행되기 전에 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e244c-186">이 방법을 사용하면 호스팅 시작으로 제공된 값보다 앱의 구성 값이 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e244c-187">호스팅 시작 어셈블리 지정</span><span class="sxs-lookup"><span data-stu-id="e244c-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e244c-188">클래스 라이브러리 또는 콘솔 앱 제공 호스팅 시작의 경우 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 호스팅 시작 어셈블리 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e244c-189">환경 변수는 세미콜론으로 구분된 어셈블리 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e244c-190">호스팅 시작 어셈블리만 `HostingStartup` 특성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-191">샘플 앱(*HostingStartupApp*)의 경우 앞에서 설명한 호스팅 시작을 검색하기 위해 환경 변수가 다음 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e244c-192">호스팅 시작 어셈블리는 호스팅 시작 어셈블리 호스트 구성 설정을 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="e244c-193">여러 개의 호스팅 시작 어셈블이 있는 경우 어셈블리가 나열되는 순서대로 해당 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e244c-194">활성화</span><span class="sxs-lookup"><span data-stu-id="e244c-194">Activation</span></span>

<span data-ttu-id="e244c-195">호스팅 시작 활성화 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e244c-196">[런타임 저장소](#runtime-store): 활성화에는 활성화를 위한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e244c-197">샘플 앱은 호스팅 시작 어셈블리 및 종속성 파일을 *배포* 폴더 안에 배치하여 다중 머신 환경에서 호스팅 시작의 배포를 용이하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e244c-198">*배포* 폴더에는 호스팅 시작을 사용하도록 배포 시스템에 환경 변수를 만들거나 수정하는 PowerShell 스크립트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e244c-199">활성화에 필요한 컴파일 시간 참조</span><span class="sxs-lookup"><span data-stu-id="e244c-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e244c-200">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="e244c-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e244c-201">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="e244c-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e244c-202">런타임 저장소</span><span class="sxs-lookup"><span data-stu-id="e244c-202">Runtime store</span></span>

<span data-ttu-id="e244c-203">호스팅 시작 구현은 [런타임 저장소](/dotnet/core/deploying/runtime-store)에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e244c-204">향상된 앱에서는 어셈블리에 대한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e244c-205">호스팅 시작이 빌드된 후에는 매니페스트 프로젝트 파일과 [dotnet store](/dotnet/core/tools/dotnet-store) 명령을 사용하여 런타임 저장소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e244c-206">샘플 앱(*RuntimeStore* 프로젝트)에서 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e244c-207">런타임에서 런타임 저장소를 검색하기 위해 런타임 저장소의 위치가 `DOTNET_SHARED_STORE` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e244c-208">**호스팅 시작의 종속성 파일 수정 및 배치**</span><span class="sxs-lookup"><span data-stu-id="e244c-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e244c-209">향상된 기능에 대한 패키지 참조 없이 향상된 기능을 활성화하려면 `additionalDeps`를 사용하여 런타임에 추가 종속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e244c-210">`additionalDeps`를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e244c-211">시작 시 앱의 고유한 *.deps.json* 파일과 병합하기 위해 추가 *.deps.json* 파일 집합을 제공하여 앱의 라이브러리 그래프를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e244c-212">호스팅 시작 어셈블리를 검색 가능하고 로드 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e244c-213">추가 종속성 파일을 생성하기 위해 권장되는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e244c-214">이전 섹션에서 참조한 런타임 저장소 매니페스트 파일에서 `dotnet publish`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e244c-215">결과 *.deps.json* 파일의 `runtime` 섹션 및 라이브러리에서 매니페스트 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e244c-216">예제 프로젝트에서 `store.manifest/1.0.0` 속성은 `targets` 및 `libraries` 섹션에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="e244c-217">*.deps.json* 파일을 다음 위치에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e244c-218">`{ADDITIONAL DEPENDENCIES PATH}`: `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가되는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e244c-219">`{SHARED FRAMEWORK NAME}`: 이 추가 종속성 파일에 대한 공유 프레임워크가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e244c-220">`{SHARED FRAMEWORK VERSION}`: 최소 공유 프레임워크 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e244c-221">`{ENHANCEMENT ASSEMBLY NAME}`: 향상된 기능의 어셈블리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e244c-222">샘플 앱(*RuntimeStore* 프로젝트)에서 추가 종속성 파일은 다음 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e244c-223">런타임에서 런타임 저장소 위치를 검색하기 위해 추가 종속성 파일 위치가 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e244c-224">샘플 앱( *RuntimeStore* 프로젝트)에서 런타임 저장소 빌드 및 추가 종속성 파일 생성은 [PowerShell](/powershell/scripting/powershell-scripting) 스크립트를 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e244c-225">다양한 운영 체제에 대한 환경 변수를 설정하는 방법의 예제는 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e244c-226">**배포**</span><span class="sxs-lookup"><span data-stu-id="e244c-226">**Deployment**</span></span>

<span data-ttu-id="e244c-227">다중 머신 환경에서 호스팅 시작의 배포를 용이하게 하기 위해 샘플 앱은 다음을 포함하는 게시된 출력에 *배포* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e244c-228">호스팅 시작 런타임 저장소.</span><span class="sxs-lookup"><span data-stu-id="e244c-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e244c-229">호스팅 시작 종속성 파일.</span><span class="sxs-lookup"><span data-stu-id="e244c-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e244c-230">호스팅 시작 활성화를 지원하기 위해 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` 및 `DOTNET_ADDITIONAL_DEPS` 를 만들거나 수정하는 PowerShell 스크립트입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e244c-231">배포 시스템의 관리 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e244c-232">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="e244c-232">NuGet package</span></span>

<span data-ttu-id="e244c-233">NuGet 패키지에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e244c-234">패키지에 `HostingStartup` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-235">패키지에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e244c-236">향상된 앱의 프로젝트 파일은 앱의 프로젝트 파일(컴파일 시간 참조)에서 호스팅 시작을 위한 패키지 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e244c-237">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e244c-238">이 방식은 [nuget.org](https://www.nuget.org/)에 게시된 호스팅 시작 어셈블리 패키지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e244c-239">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="e244c-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e244c-240">NuGet 패키지 및 런타임 저장소에 대한 자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e244c-241">플랫폼 간 도구로 NuGet 패키지를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="e244c-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e244c-242">패키지 게시</span><span class="sxs-lookup"><span data-stu-id="e244c-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e244c-243">런타임 패키지 저장소</span><span class="sxs-lookup"><span data-stu-id="e244c-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e244c-244">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="e244c-244">Project bin folder</span></span>

<span data-ttu-id="e244c-245">호스팅 시작 향상 기능은 향상된 앱의 *bin* 배포 어셈블리에 의해 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e244c-246">어셈블리에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e244c-247">향상된 앱의 프로젝트 파일은 호스팅 시작에 대한 어셈블리 참조를 만듭니다(컴파일 시간 참조).</span><span class="sxs-lookup"><span data-stu-id="e244c-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e244c-248">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e244c-249">이 방법은 배포 시나리오에서 호스팅 시작 어셈블리( *.dll* 파일)에 대한 컴파일 시간 참조를 수행하고 어셈블리를 다음 중 하나로 이동해야 할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e244c-250">사용하는 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-250">The consuming project.</span></span>
  * <span data-ttu-id="e244c-251">사용하는 프로젝트에서 액세스할 수 있는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e244c-252">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="e244c-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e244c-253">.NET Framework를 대상으로 지정하는 경우 어셈블리는 기본 로드 컨텍스트에 로드될 수 있습니다. 즉, .NET Framework는 어셈블리가 다음 위치 중 하나에 있는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e244c-254">애플리케이션 기본 경로: 애플리케이션의 실행 파일( *.exe*)이 있는 *bin* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e244c-255">GAC(전역 어셈블리 캐시): GAC에는 여러 .NET Framework 앱이 공유하는 어셈블리가 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e244c-256">자세한 내용은 [방법: .NET Framework 설명서의 전역 어셈블리 캐시](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)에 어셈블리를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e244c-257">샘플 코드</span><span class="sxs-lookup"><span data-stu-id="e244c-257">Sample code</span></span>

<span data-ttu-id="e244c-258">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))는 호스팅 시작 구현 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e244c-259">두 개의 호스팅 시작 어셈블리(클래스 라이브러리)는 각각 한 쌍의 메모리 내 구성 키-값 쌍을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e244c-260">NuGet 패키지(*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="e244c-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e244c-261">클래스 라이브러리(*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="e244c-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e244c-262">호스팅 시작은 런타임 저장소 배포 어셈블리에서 활성화됩니다(*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="e244c-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e244c-263">어셈블리는 시작 시 다음과 같은 진단 정보를 제공하는 두 개의 미들웨어를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e244c-264">등록된 서비스</span><span class="sxs-lookup"><span data-stu-id="e244c-264">Registered services</span></span>
  * <span data-ttu-id="e244c-265">주소(구성표, 호스트, 기본 경로, 경로, 쿼리 문자열)</span><span class="sxs-lookup"><span data-stu-id="e244c-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e244c-266">연결(원격 IP, 원격 포트, 로컬 IP, 로컬 포트, 클라이언트 인증서)</span><span class="sxs-lookup"><span data-stu-id="e244c-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e244c-267">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="e244c-267">Request headers</span></span>
  * <span data-ttu-id="e244c-268">환경 변수</span><span class="sxs-lookup"><span data-stu-id="e244c-268">Environment variables</span></span>

<span data-ttu-id="e244c-269">샘플을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="e244c-269">To run the sample:</span></span>

<span data-ttu-id="e244c-270">**NuGet 패키지에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="e244c-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e244c-271">[dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 *HostingStartupPackage* 패키지를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e244c-272">*HostingStartupPackage* 패키지의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e244c-273">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-273">Compile and run the app.</span></span> <span data-ttu-id="e244c-274">패키지 참조가 향상된 앱(컴파일 시간 참조)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e244c-275">앱의 프로젝트 파일에 있는 `<PropertyGroup>`에서는 패키지 프로젝트의 출력( *../HostingStartupPackage/bin/Debug*)을 패키지 원본으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e244c-276">이렇게 하면 [nuget.org](https://www.nuget.org/)에 패키지를 업로드하지 않고 패키지를 사용할 수 있습니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e244c-277">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 패키지의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e244c-278">*HostingStartupPackage* 프로젝트를 변경하고 다시 컴파일하는 경우, 로컬 NuGet 패키지 캐시의 선택을 취소하여 *HostingStartupApp* 이 로컬 캐시에서 부실 패키지가 아닌 업데이트된 패키지를 수신하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e244c-279">로컬 NuGet 캐시를 지우려면 다음 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e244c-280">**클래스 라이브러리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="e244c-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="e244c-281">[dotnet build](/dotnet/core/tools/dotnet-build) 명령을 사용하여 *HostingStartupLibrary* 클래스 라이브러리를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e244c-282">*HostingStartupLibrary* 클래스 라이브러리의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e244c-283">*bin* - *HostingStartupLibrary.dll* 파일을 클래스 라이브러리의 컴파일된 출력에서 앱의 *bin/Debug* 폴더로 복사하여 클래스 라이브러리의 어셈블리를 앱에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e244c-284">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-284">Compile and run the app.</span></span> <span data-ttu-id="e244c-285">앱의 프로젝트 파일에 있는 `<ItemGroup>`은 클래스 라이브러리의 어셈블리( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*)(컴파일 시간 참조)를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e244c-286">자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e244c-287">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 클래스 라이브러리의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e244c-288">**런타임 저장소 배포 어셈블리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="e244c-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e244c-289">*StartupDiagnostics* 프로젝트는 [PowerShell](/powershell/scripting/powershell-scripting)을 사용하여 해당 *StartupDiagnostics.deps.json* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e244c-290">PowerShell은 Windows 7 SP1 및 Windows Server 2008 R2 SP1부터 Windows에서 기본적으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e244c-291">다른 플랫폼에서 PowerShell을 가져오려면 [다양한 버전의 PowerShell 설치](/powershell/scripting/install/installing-powershell)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e244c-292">*RuntimeStore* 폴더에서 *build.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e244c-293">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-293">The script:</span></span>
   * <span data-ttu-id="e244c-294">*obj\packages* 폴더에 `StartupDiagnostics` 패키지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e244c-295">*store* 폴더에서 `StartupDiagnostics`의 런타임 저장소를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e244c-296">해당 스크립트에서 `dotnet store` 명령은 Windows에 배포된 호스팅 시작에서 `win7-x64` [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e244c-297">다른 런타임에 호스팅 시작을 제공할 때 스크립트의 줄 37에서 올바른 RID로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e244c-298">`StartupDiagnostics`의 런타임 저장소는 나중에 어셈블리가 사용될 컴퓨터의 사용자 또는 시스템의 런타임 저장소로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e244c-299">`StartupDiagnostics` 어셈블리의 사용자 런타임 저장소 설치 위치는 *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll* 입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e244c-300">*additionalDeps* 폴더에 `StartupDiagnostics`의 `additionalDeps`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e244c-301">추가 종속성은 나중에 사용자 또는 시스템의 추가 종속성으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e244c-302">사용자 `StartupDiagnostics` 추가 종속성 설치 위치는 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json* 입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e244c-303">*deployment* 폴더에 *deploy.ps1* 파일을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e244c-304">*배포* 폴더에서 *deploy.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e244c-305">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-305">The script appends:</span></span>
   * <span data-ttu-id="e244c-306">`StartupDiagnostics`를 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e244c-307">`DOTNET_ADDITIONAL_DEPS` 환경 변수의 호스팅 시작 종속성 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="e244c-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e244c-308">`DOTNET_SHARED_STORE` 환경 변수의 런타임 저장소 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="e244c-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e244c-309">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-309">Run the sample app.</span></span>
1. <span data-ttu-id="e244c-310">`/services` 엔드포인트가 앱의 등록된 서비스를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e244c-311">`/diag` 엔드포인트가 진단 정보를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e244c-312"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>(호스팅 시작) 구현에서는 외부 어셈블리에서 시작할 때 앱에 향상된 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="e244c-313">예를 들어 외부 라이브러리는 호스팅 시작 구현을 사용하여 앱에 추가 구성 공급자 또는 서비스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="e244c-314">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e244c-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="e244c-315">HostingStartup 특성</span><span class="sxs-lookup"><span data-stu-id="e244c-315">HostingStartup attribute</span></span>

<span data-ttu-id="e244c-316">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 런타임에 활성화할 호스팅 시작 어셈블리의 현재 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="e244c-317">항목 어셈블리 또는 `Startup` 클래스가 포함된 어셈블리에서는 `HostingStartup` 특성을 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-318">`HostingStartup` 특성을 검색할 어셈블리 목록은 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey)의 구성에서 런타임에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="e244c-319">검색에서 제외할 어셈블리 목록은 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey)에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="e244c-320">자세한 내용은 [웹 호스트: 호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 및 [웹 호스트: 호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="e244c-321">다음 예제에서는 호스팅 시작 어셈블리의 네임스페이스가 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="e244c-322">호스팅 시작 코드를 포함하는 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e244c-323">`HostingStartup` 특성은 일반적으로 호스팅 시작 어셈블리의 `IHostingStartup` 구현 클래스 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="e244c-324">로드된 호스팅 시작 어셈블리 검색</span><span class="sxs-lookup"><span data-stu-id="e244c-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="e244c-325">로드된 호스팅 시작 어셈블리를 검색하려면 로깅을 사용하도록 설정하고 앱의 로그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="e244c-326">어셈블리를 로드할 때 발생하는 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="e244c-327">로드된 호스팅 시작 어셈블리는 디버그 수준에서 기록되고 모든 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="e244c-328">호스팅 시작 어셈블리의 자동 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="e244c-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="e244c-329">호스팅 시작 어셈블리의 자동 로딩을 사용하지 않으려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="e244c-330">모든 호스팅 시작 어셈블리가 로드되지 않도록 하려면 다음 중 하나를 `true` 또는 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="e244c-331">[호스팅 시작 방지](xref:fundamentals/host/web-host#prevent-hosting-startup) 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="e244c-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="e244c-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="e244c-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="e244c-333">특정 호스팅 시작 어셈블리가 로드되지 않도록 하려면 시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열로 다음 중 하나를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="e244c-334">[호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="e244c-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="e244c-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="e244c-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="e244c-336">호스트 구성 설정과 환경 변수가 모두 설정되면 호스트 설정이 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="e244c-337">호스트 설정 또는 환경 변수를 사용하여 호스팅 시작 어셈블리를 사용하지 않도록 설정하면 어셈블리가 전역적으로 해제되고 앱의 여러 특징을 해제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="e244c-338">프로젝트</span><span class="sxs-lookup"><span data-stu-id="e244c-338">Project</span></span>

<span data-ttu-id="e244c-339">다음 프로젝트 형식 중 하나를 사용하여 호스팅 시작을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="e244c-340">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e244c-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="e244c-341">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="e244c-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="e244c-342">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="e244c-342">Class library</span></span>

<span data-ttu-id="e244c-343">클래스 라이브러리에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="e244c-344">라이브러리에는 `HostingStartup` 특성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e244c-345">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 Razor 페이지 앱, *HostingStartupApp* 및 클래스 라이브러리, *HostingStartupLibrary* 가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="e244c-346">클래스 라이브러리:</span><span class="sxs-lookup"><span data-stu-id="e244c-346">The class library:</span></span>

* <span data-ttu-id="e244c-347">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e244c-348">`ServiceKeyInjection`은 메모리 내 구성 공급자([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*))를 사용하여 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="e244c-349">호스팅 시작의 네임스페이스 및 클래스를 식별하는 `HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="e244c-350">`ServiceKeyInjection` 클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="e244c-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e244c-352">앱의 인덱스 페이지는 클래스 라이브러리의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="e244c-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="e244c-354">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 별도의 호스팅 시작인 *HostingStartupPackage* 를 제공하는 NuGet 패키지 프로젝트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="e244c-355">패키지는 앞에서 설명한 클래스 라이브러리와 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="e244c-356">패키지:</span><span class="sxs-lookup"><span data-stu-id="e244c-356">The package:</span></span>

* <span data-ttu-id="e244c-357">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="e244c-358">`ServiceKeyInjection`은 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="e244c-359">`HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="e244c-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="e244c-361">앱의 인덱스 페이지는 패키지의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="e244c-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="e244c-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="e244c-363">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="e244c-363">Console app without an entry point</span></span>

<span data-ttu-id="e244c-364">*이 방법은 .NET Framework가 아닌 .NET Core 앱에서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="e244c-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="e244c-365">활성화에 대한 컴파일 시간 참조가 필요하지 않은 동적 호스팅 시작 기능 향상은 `HostingStartup` 특성이 포함되는 진입점 없는 콘솔 앱에서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-366">콘솔 앱을 게시하면 런타임 저장소에서 사용할 수 있는 호스팅 시작 어셈블리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="e244c-367">다음과 같은 이유로 이 프로세스에서는 진입점 없는 콘솔 앱이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="e244c-368">호스팅 시작 어셈블리에서 호스팅 시작을 사용하려면 종속성 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="e244c-369">종속성 파일은 라이브러리가 아닌 앱을 게시하여 생성된 실행 가능한 앱 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="e244c-370">라이브러리를 [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)에 직접 추가할 수 없습니다. 이 저장소에는 공유 런타임을 대상으로 하는 실행 가능한 프로젝트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="e244c-371">동적 호스팅 시작 만들기:</span><span class="sxs-lookup"><span data-stu-id="e244c-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="e244c-372">호스팅 시작 어셈블리는 다음과 같은 진입점 없는 콘솔 앱에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="e244c-373">`IHostingStartup` 구현이 포함되는 클래스를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="e244c-374">`IHostingStartup` 구현 클래스를 식별하는 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="e244c-375">콘솔 앱은 호스팅 시작의 종속성을 얻기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="e244c-376">콘솔 앱을 게시하면 사용되지 않는 종속성이 종속성 파일에서 트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="e244c-377">종속성 파일은 호스팅 시작 어셈블리의 런타임 위치를 설정하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="e244c-378">호스팅 시작 어셈블리 및 해당 종속성 파일은 런타임 패키지 저장소에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="e244c-379">호스팅 시작 어셈블리 및 해당 종속성 파일을 검색하기 위해 한 쌍의 환경 변수에 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="e244c-380">콘솔 앱은 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="e244c-381">[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 특성은 <xref:Microsoft.AspNetCore.Hosting.IWebHost>를 빌드할 때 로드 및 실행을 위해 `IHostingStartup`의 구현으로 클래스를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="e244c-382">다음 예제에서 네임스페이스는 `StartupEnhancement`이고 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="e244c-383">클래스는 `IHostingStartup`을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="e244c-384">클래스의 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드는 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>를 사용하여 향상된 기능을 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="e244c-385">호스팅 시작 어셈블리의 `IHostingStartup.Configure`는 사용자 코드로 `Startup.Configure` 전에 런타임에서 호출됩니다. 그러면 사용자 코드가 호스팅 시작 어셈블리에서 제공하는 모든 구성을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="e244c-386">`IHostingStartup` 프로젝트를 빌드할 때 종속성 파일( *.deps.json*)은 어셈블리의 `runtime` 위치를 *bin* 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="e244c-387">파일의 일부만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-387">Only part of the file is shown.</span></span> <span data-ttu-id="e244c-388">이 예제의 어셈블리 이름은 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="e244c-389">호스팅 시작으로 제공하는 구성</span><span class="sxs-lookup"><span data-stu-id="e244c-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="e244c-390">호스팅 시작의 구성을 우선할지, 앱의 구성을 우선할지에 따라 두 가지 방법으로 구성을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="e244c-391">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행된 후 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e244c-392">이 방법을 사용하면 호스팅 시작 구성이 앱의 구성보다 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="e244c-393">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행되기 전에 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="e244c-394">이 방법을 사용하면 호스팅 시작으로 제공된 값보다 앱의 구성 값이 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="e244c-395">호스팅 시작 어셈블리 지정</span><span class="sxs-lookup"><span data-stu-id="e244c-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="e244c-396">클래스 라이브러리 또는 콘솔 앱 제공 호스팅 시작의 경우 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 호스팅 시작 어셈블리 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="e244c-397">환경 변수는 세미콜론으로 구분된 어셈블리 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="e244c-398">호스팅 시작 어셈블리만 `HostingStartup` 특성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-399">샘플 앱(*HostingStartupApp*)의 경우 앞에서 설명한 호스팅 시작을 검색하기 위해 환경 변수가 다음 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="e244c-400">호스팅 시작 어셈블리는 [호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 호스트 구성 설정을 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="e244c-401">여러 개의 호스팅 시작 어셈블이 있는 경우 어셈블리가 나열되는 순서대로 해당 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 메서드가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="e244c-402">활성화</span><span class="sxs-lookup"><span data-stu-id="e244c-402">Activation</span></span>

<span data-ttu-id="e244c-403">호스팅 시작 활성화 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="e244c-404">[런타임 저장소](#runtime-store): 활성화에는 활성화를 위한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="e244c-405">샘플 앱은 호스팅 시작 어셈블리 및 종속성 파일을 *배포* 폴더 안에 배치하여 다중 머신 환경에서 호스팅 시작의 배포를 용이하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="e244c-406">*배포* 폴더에는 호스팅 시작을 사용하도록 배포 시스템에 환경 변수를 만들거나 수정하는 PowerShell 스크립트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="e244c-407">활성화에 필요한 컴파일 시간 참조</span><span class="sxs-lookup"><span data-stu-id="e244c-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="e244c-408">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="e244c-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="e244c-409">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="e244c-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="e244c-410">런타임 저장소</span><span class="sxs-lookup"><span data-stu-id="e244c-410">Runtime store</span></span>

<span data-ttu-id="e244c-411">호스팅 시작 구현은 [런타임 저장소](/dotnet/core/deploying/runtime-store)에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="e244c-412">향상된 앱에서는 어셈블리에 대한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="e244c-413">호스팅 시작이 빌드된 후에는 매니페스트 프로젝트 파일과 [dotnet store](/dotnet/core/tools/dotnet-store) 명령을 사용하여 런타임 저장소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="e244c-414">샘플 앱(*RuntimeStore* 프로젝트)에서 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="e244c-415">런타임에서 런타임 저장소를 검색하기 위해 런타임 저장소의 위치가 `DOTNET_SHARED_STORE` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="e244c-416">**호스팅 시작의 종속성 파일 수정 및 배치**</span><span class="sxs-lookup"><span data-stu-id="e244c-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="e244c-417">향상된 기능에 대한 패키지 참조 없이 향상된 기능을 활성화하려면 `additionalDeps`를 사용하여 런타임에 추가 종속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="e244c-418">`additionalDeps`를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="e244c-419">시작 시 앱의 고유한 *.deps.json* 파일과 병합하기 위해 추가 *.deps.json* 파일 집합을 제공하여 앱의 라이브러리 그래프를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="e244c-420">호스팅 시작 어셈블리를 검색 가능하고 로드 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="e244c-421">추가 종속성 파일을 생성하기 위해 권장되는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="e244c-422">이전 섹션에서 참조한 런타임 저장소 매니페스트 파일에서 `dotnet publish`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="e244c-423">결과 *.deps.json* 파일의 `runtime` 섹션 및 라이브러리에서 매니페스트 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="e244c-424">예제 프로젝트에서 `store.manifest/1.0.0` 속성은 `targets` 및 `libraries` 섹션에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="e244c-425">*.deps.json* 파일을 다음 위치에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="e244c-426">`{ADDITIONAL DEPENDENCIES PATH}`: `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가되는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="e244c-427">`{SHARED FRAMEWORK NAME}`: 이 추가 종속성 파일에 대한 공유 프레임워크가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="e244c-428">`{SHARED FRAMEWORK VERSION}`: 최소 공유 프레임워크 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="e244c-429">`{ENHANCEMENT ASSEMBLY NAME}`: 향상된 기능의 어셈블리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="e244c-430">샘플 앱(*RuntimeStore* 프로젝트)에서 추가 종속성 파일은 다음 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="e244c-431">런타임에서 런타임 저장소 위치를 검색하기 위해 추가 종속성 파일 위치가 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="e244c-432">샘플 앱( *RuntimeStore* 프로젝트)에서 런타임 저장소 빌드 및 추가 종속성 파일 생성은 [PowerShell](/powershell/scripting/powershell-scripting) 스크립트를 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="e244c-433">다양한 운영 체제에 대한 환경 변수를 설정하는 방법의 예제는 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="e244c-434">**배포**</span><span class="sxs-lookup"><span data-stu-id="e244c-434">**Deployment**</span></span>

<span data-ttu-id="e244c-435">다중 머신 환경에서 호스팅 시작의 배포를 용이하게 하기 위해 샘플 앱은 다음을 포함하는 게시된 출력에 *배포* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="e244c-436">호스팅 시작 런타임 저장소.</span><span class="sxs-lookup"><span data-stu-id="e244c-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="e244c-437">호스팅 시작 종속성 파일.</span><span class="sxs-lookup"><span data-stu-id="e244c-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="e244c-438">호스팅 시작 활성화를 지원하기 위해 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` 및 `DOTNET_ADDITIONAL_DEPS` 를 만들거나 수정하는 PowerShell 스크립트입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="e244c-439">배포 시스템의 관리 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="e244c-440">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="e244c-440">NuGet package</span></span>

<span data-ttu-id="e244c-441">NuGet 패키지에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="e244c-442">패키지에 `HostingStartup` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="e244c-443">패키지에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="e244c-444">향상된 앱의 프로젝트 파일은 앱의 프로젝트 파일(컴파일 시간 참조)에서 호스팅 시작을 위한 패키지 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="e244c-445">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e244c-446">이 방식은 [nuget.org](https://www.nuget.org/)에 게시된 호스팅 시작 어셈블리 패키지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="e244c-447">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="e244c-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="e244c-448">NuGet 패키지 및 런타임 저장소에 대한 자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="e244c-449">플랫폼 간 도구로 NuGet 패키지를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="e244c-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="e244c-450">패키지 게시</span><span class="sxs-lookup"><span data-stu-id="e244c-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="e244c-451">런타임 패키지 저장소</span><span class="sxs-lookup"><span data-stu-id="e244c-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="e244c-452">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="e244c-452">Project bin folder</span></span>

<span data-ttu-id="e244c-453">호스팅 시작 향상 기능은 향상된 앱의 *bin* 배포 어셈블리에 의해 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="e244c-454">어셈블리에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="e244c-455">향상된 앱의 프로젝트 파일은 호스팅 시작에 대한 어셈블리 참조를 만듭니다(컴파일 시간 참조).</span><span class="sxs-lookup"><span data-stu-id="e244c-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="e244c-456">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="e244c-457">이 방법은 배포 시나리오에서 호스팅 시작 어셈블리( *.dll* 파일)에 대한 컴파일 시간 참조를 수행하고 어셈블리를 다음 중 하나로 이동해야 할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="e244c-458">사용하는 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-458">The consuming project.</span></span>
  * <span data-ttu-id="e244c-459">사용하는 프로젝트에서 액세스할 수 있는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="e244c-460">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="e244c-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="e244c-461">.NET Framework를 대상으로 지정하는 경우 어셈블리는 기본 로드 컨텍스트에 로드될 수 있습니다. 즉, .NET Framework는 어셈블리가 다음 위치 중 하나에 있는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="e244c-462">애플리케이션 기본 경로: 애플리케이션의 실행 파일( *.exe*)이 있는 *bin* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="e244c-463">GAC(전역 어셈블리 캐시): GAC에는 여러 .NET Framework 앱이 공유하는 어셈블리가 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="e244c-464">자세한 내용은 [방법: .NET Framework 설명서의 전역 어셈블리 캐시](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)에 어셈블리를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="e244c-465">샘플 코드</span><span class="sxs-lookup"><span data-stu-id="e244c-465">Sample code</span></span>

<span data-ttu-id="e244c-466">[샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))는 호스팅 시작 구현 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="e244c-467">두 개의 호스팅 시작 어셈블리(클래스 라이브러리)는 각각 한 쌍의 메모리 내 구성 키-값 쌍을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="e244c-468">NuGet 패키지(*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="e244c-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="e244c-469">클래스 라이브러리(*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="e244c-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="e244c-470">호스팅 시작은 런타임 저장소 배포 어셈블리에서 활성화됩니다(*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="e244c-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="e244c-471">어셈블리는 시작 시 다음과 같은 진단 정보를 제공하는 두 개의 미들웨어를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="e244c-472">등록된 서비스</span><span class="sxs-lookup"><span data-stu-id="e244c-472">Registered services</span></span>
  * <span data-ttu-id="e244c-473">주소(구성표, 호스트, 기본 경로, 경로, 쿼리 문자열)</span><span class="sxs-lookup"><span data-stu-id="e244c-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="e244c-474">연결(원격 IP, 원격 포트, 로컬 IP, 로컬 포트, 클라이언트 인증서)</span><span class="sxs-lookup"><span data-stu-id="e244c-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="e244c-475">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="e244c-475">Request headers</span></span>
  * <span data-ttu-id="e244c-476">환경 변수</span><span class="sxs-lookup"><span data-stu-id="e244c-476">Environment variables</span></span>

<span data-ttu-id="e244c-477">샘플을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="e244c-477">To run the sample:</span></span>

<span data-ttu-id="e244c-478">**NuGet 패키지에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="e244c-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="e244c-479">[dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 *HostingStartupPackage* 패키지를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="e244c-480">*HostingStartupPackage* 패키지의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e244c-481">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-481">Compile and run the app.</span></span> <span data-ttu-id="e244c-482">패키지 참조가 향상된 앱(컴파일 시간 참조)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="e244c-483">앱의 프로젝트 파일에 있는 `<PropertyGroup>`에서는 패키지 프로젝트의 출력( *../HostingStartupPackage/bin/Debug*)을 패키지 원본으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="e244c-484">이렇게 하면 [nuget.org](https://www.nuget.org/)에 패키지를 업로드하지 않고 패키지를 사용할 수 있습니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="e244c-485">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 패키지의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e244c-486">*HostingStartupPackage* 프로젝트를 변경하고 다시 컴파일하는 경우, 로컬 NuGet 패키지 캐시의 선택을 취소하여 *HostingStartupApp* 이 로컬 캐시에서 부실 패키지가 아닌 업데이트된 패키지를 수신하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="e244c-487">로컬 NuGet 캐시를 지우려면 다음 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="e244c-488">**클래스 라이브러리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="e244c-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="e244c-489">[dotnet build](/dotnet/core/tools/dotnet-build) 명령을 사용하여 *HostingStartupLibrary* 클래스 라이브러리를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="e244c-490">*HostingStartupLibrary* 클래스 라이브러리의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="e244c-491">*bin* - *HostingStartupLibrary.dll* 파일을 클래스 라이브러리의 컴파일된 출력에서 앱의 *bin/Debug* 폴더로 복사하여 클래스 라이브러리의 어셈블리를 앱에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="e244c-492">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-492">Compile and run the app.</span></span> <span data-ttu-id="e244c-493">앱의 프로젝트 파일에 있는 `<ItemGroup>`는 클래스 라이브러리의 어셈블리( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (컴파일 시간 참조)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="e244c-494">자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="e244c-495">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 클래스 라이브러리의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="e244c-496">**런타임 저장소 배포 어셈블리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="e244c-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="e244c-497">*StartupDiagnostics* 프로젝트는 [PowerShell](/powershell/scripting/powershell-scripting)을 사용하여 해당 *StartupDiagnostics.deps.json* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="e244c-498">PowerShell은 Windows 7 SP1 및 Windows Server 2008 R2 SP1부터 Windows에서 기본적으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="e244c-499">다른 플랫폼에서 PowerShell을 가져오려면 [다양한 버전의 PowerShell 설치](/powershell/scripting/install/installing-powershell)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e244c-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="e244c-500">*RuntimeStore* 폴더에서 *build.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="e244c-501">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-501">The script:</span></span>
   * <span data-ttu-id="e244c-502">*obj\packages* 폴더에 `StartupDiagnostics` 패키지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="e244c-503">*store* 폴더에서 `StartupDiagnostics`의 런타임 저장소를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="e244c-504">해당 스크립트에서 `dotnet store` 명령은 Windows에 배포된 호스팅 시작에서 `win7-x64` [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="e244c-505">다른 런타임에 호스팅 시작을 제공할 때 스크립트의 줄 37에서 올바른 RID로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="e244c-506">`StartupDiagnostics`의 런타임 저장소는 나중에 어셈블리가 사용될 컴퓨터의 사용자 또는 시스템의 런타임 저장소로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="e244c-507">`StartupDiagnostics` 어셈블리의 사용자 런타임 저장소 설치 위치는 *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll* 입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="e244c-508">*additionalDeps* 폴더에 `StartupDiagnostics`의 `additionalDeps`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="e244c-509">추가 종속성은 나중에 사용자 또는 시스템의 추가 종속성으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="e244c-510">사용자 `StartupDiagnostics` 추가 종속성 설치 위치는 *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json* 입니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="e244c-511">*deployment* 폴더에 *deploy.ps1* 파일을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="e244c-512">*배포* 폴더에서 *deploy.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="e244c-513">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-513">The script appends:</span></span>
   * <span data-ttu-id="e244c-514">`StartupDiagnostics`를 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="e244c-515">`DOTNET_ADDITIONAL_DEPS` 환경 변수의 호스팅 시작 종속성 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="e244c-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="e244c-516">`DOTNET_SHARED_STORE` 환경 변수의 런타임 저장소 경로(RuntimeStore 프로젝트의 *deployment* 폴더에 있음).</span><span class="sxs-lookup"><span data-stu-id="e244c-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="e244c-517">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-517">Run the sample app.</span></span>
1. <span data-ttu-id="e244c-518">`/services` 엔드포인트가 앱의 등록된 서비스를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="e244c-519">`/diag` 엔드포인트가 진단 정보를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e244c-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
