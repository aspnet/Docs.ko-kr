---
title: ASP.NET Core 웹 호스트
author: rick-anderson
description: 앱 시작 및 수명 관리를 담당하는 ASP.NET Core의 웹 호스트에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 09383cb9067d7fdc2d7b69213b741e7ae823e9ea
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060016"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="d85f0-103">ASP.NET Core 웹 호스트</span><span class="sxs-lookup"><span data-stu-id="d85f0-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="d85f0-104">ASP.NET Core 앱은 *호스트* 를 구성 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="d85f0-105">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="d85f0-106">최소한으로 호스트는 서버 및 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="d85f0-107">호스트는 로깅, 종속성 주입 및 구성을 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d85f0-108">이 문서에서는 이전 버전과의 호환성을 위해서만 사용 가능한 상태를 유지하는 웹 호스트에 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="d85f0-109">모든 앱 유형에 [일반 호스트](xref:fundamentals/host/generic-host)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d85f0-110">이 문서에서는 웹앱 호스트를 위한 웹 호스트에 대해 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="d85f0-111">다른 종류의 앱인 경우 [일반 호스트](xref:fundamentals/host/generic-host)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="d85f0-112">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="d85f0-112">Set up a host</span></span>

<span data-ttu-id="d85f0-113">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)의 인스턴스를 사용하여 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="d85f0-114">이는 일반적으로 앱의 진입점인 `Main` 메서드에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="d85f0-115">프로젝트 템플릿에서 `Main`은 *Program.cs* 에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="d85f0-116">일반적인 앱은 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)를 호출하여 호스트 설정을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="d85f0-117">`CreateDefaultBuilder`를 호출하는 코드는 `CreateWebHostBuilder`라는 메서드에 포함되어, 빌더 개체에서 `Run`을 호출하는 `Main`의 코드와 분리됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="d85f0-118">이 분리는 [Entity Framework Core 도구](/ef/core/miscellaneous/cli/)를 사용하는 경우 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="d85f0-119">이 도구는 앱을 실행하지 않고 호스트를 구성하기 위해 디자인 타임에 호출할 수 있는 `CreateWebHostBuilder` 메서드를 찾으려 합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="d85f0-120">`IDesignTimeDbContextFactory`를 구현하는 방법도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="d85f0-121">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="d85f0-122">`CreateDefaultBuilder`는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="d85f0-123">앱의 호스팅 구성 공급자를 사용하여 [Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="d85f0-124">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d85f0-125">[콘텐츠 루트](xref:fundamentals/index#content-root)를 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="d85f0-126">다음에서 [호스트 구성](#host-configuration-values)을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="d85f0-127">접두사가 `ASPNETCORE_`인 환경 변수(예: `ASPNETCORE_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="d85f0-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="d85f0-128">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="d85f0-128">Command-line arguments.</span></span>
* <span data-ttu-id="d85f0-129">다음 순서대로 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="d85f0-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d85f0-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="d85f0-131">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="d85f0-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d85f0-132">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d85f0-132">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="d85f0-133">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="d85f0-133">Environment variables.</span></span>
  * <span data-ttu-id="d85f0-134">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="d85f0-134">Command-line arguments.</span></span>
* <span data-ttu-id="d85f0-135">콘솔 및 디버그 출력에 대한 [로깅](xref:fundamentals/logging/index)을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="d85f0-136">로깅은 *appsettings.json* 또는 *appsettings.{Environment}.json* 파일의 로깅 구성 섹션에 지정된 [로그 필터링](xref:fundamentals/logging/index#log-filtering) 규칙을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="d85f0-137">[ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 IIS에서 실행하는 경우 `CreateDefaultBuilder`는 앱의 기본 주소와 포트를 구성하는 [IIS 통합](xref:host-and-deploy/iis/index)을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="d85f0-138">또한 IIS 통합은 [시작 오류를 캡처](#capture-startup-errors)하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="d85f0-139">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="d85f0-140">앱의 환경이 Development인 경우 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes)를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="d85f0-141">자세한 내용은 [범위 유효성 검사](#scope-validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="d85f0-142">`CreateDefaultBuilder`에 의해 정의된 구성은 [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), 기타 메서드 및 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)의 확장 메서드로 재정의되고 확대될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="d85f0-143">몇 가지 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-143">A few examples follow:</span></span>

* <span data-ttu-id="d85f0-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)은 앱에 대한 추가 `IConfiguration`을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="d85f0-145">다음 `ConfigureAppConfiguration` 호출은 *appsettings.xml* 파일에 앱 구성을 포함하는 대리자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="d85f0-146">`ConfigureAppConfiguration`이 여러 번 호출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="d85f0-147">이 구성은 호스트(예: 서버 URL 또는 환경)에 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="d85f0-148">[호스트 구성 값](#host-configuration-values) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="d85f0-149">다음 `ConfigureLogging` 호출은 최소 로깅 수준([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel))을 [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel)으로 구성하는 대리자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="d85f0-150">이 설정은 `CreateDefaultBuilder`에 의해 구성된 *appsettings.Development.json* (`LogLevel.Debug`) 및 *appsettings.Production.json* (`LogLevel.Error`)의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d85f0-151">`ConfigureLogging`이 여러 번 호출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d85f0-152">`ConfigureKestrel`에 대한 다음 호출은 Kestrel이 `CreateDefaultBuilder`에 의해 구성될 때 설정된 30,000,000바이트의 기본 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="d85f0-153">[UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel)에 대한 다음 호출은 Kestrel이 `CreateDefaultBuilder`에 의해 구성될 때 설정된 30,000,000바이트의 기본 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="d85f0-154">[콘텐츠 루트](xref:fundamentals/index#content-root)는 호스트가 MVC 뷰 파일과 같은 콘텐츠 파일을 검색하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="d85f0-155">앱이 프로젝트의 루트 폴더에서 시작되면 프로젝트의 루트 폴더가 콘텐츠 루트로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="d85f0-156">이것이 [Visual Studio](https://visualstudio.microsoft.com) 및 [dotnet 새 템플릿](/dotnet/core/tools/dotnet-new)에서 사용되는 기본값입니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="d85f0-157">앱 구성에 대한 자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="d85f0-158">ASP.NET Core 2.x에서는 정적 `CreateDefaultBuilder` 메서드 사용에 대한 대안으로 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)에서 호스트를 만들 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="d85f0-159">호스트를 설정할 때 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) 및 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="d85f0-160">`Startup` 클래스가 지정된 경우 `Configure` 메서드를 정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="d85f0-161">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="d85f0-162">`ConfigureServices`에 대한 여러 호출은 서로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="d85f0-163">`WebHostBuilder`에서 `Configure` 또는 `UseStartup`에 대한 여러 호출은 이전 설정을 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="d85f0-164">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="d85f0-164">Host configuration values</span></span>

<span data-ttu-id="d85f0-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)는 호스트 구성 값을 설정하기 위해 다음 방법을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="d85f0-166">`ASPNETCORE_{configurationKey}` 형식의 환경 변수를 포함하는 호스트 빌더 구성.</span><span class="sxs-lookup"><span data-stu-id="d85f0-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="d85f0-167">예: `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="d85f0-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="d85f0-168">[UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) 및 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 같은 확장([구성 재정의](#override-configuration) 섹션을 참조하세요).</span><span class="sxs-lookup"><span data-stu-id="d85f0-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="d85f0-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) 및 연결된 키.</span><span class="sxs-lookup"><span data-stu-id="d85f0-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="d85f0-170">`UseSetting`을 사용하여 값을 설정할 때 값은 형식에 관계 없이 문자열로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="d85f0-171">호스트는 마지막에 값을 설정한 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="d85f0-172">자세한 내용은 다음 섹션의 [구성 재정의](#override-configuration)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d85f0-173">애플리케이션 키(이름)</span><span class="sxs-lookup"><span data-stu-id="d85f0-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d85f0-174">[UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 또는 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure)가 호스트 생성 중에 호출되는 경우 `IWebHostEnvironment.ApplicationName` 속성이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="d85f0-175">해당 값은 앱의 진입점을 포함하는 어셈블리의 이름으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="d85f0-176">값을 명시적으로 설정하려면 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d85f0-177">[UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 또는 [구성](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure)이 호스트 생성 중에 호출되는 경우 [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) 속성이 자동으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="d85f0-178">해당 값은 앱의 진입점을 포함하는 어셈블리의 이름으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="d85f0-179">값을 명시적으로 설정하려면 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="d85f0-180">**키** : applicationName</span><span class="sxs-lookup"><span data-stu-id="d85f0-180">**Key** : applicationName</span></span>  
<span data-ttu-id="d85f0-181">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-181">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-182">**기본값** : 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-182">**Default** : The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d85f0-183">**설정 방법** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d85f0-183">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="d85f0-184">**환경 변수** : `ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d85f0-184">**Environment variable** : `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="d85f0-185">시작 오류 캡처</span><span class="sxs-lookup"><span data-stu-id="d85f0-185">Capture Startup Errors</span></span>

<span data-ttu-id="d85f0-186">이 설정은 시작 오류의 캡처를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="d85f0-187">**키** : captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d85f0-187">**Key** : captureStartupErrors</span></span>  
<span data-ttu-id="d85f0-188">**형식** : *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="d85f0-188">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d85f0-189">**기본값** : 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-189">**Default** : Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d85f0-190">**설정 방법** : `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d85f0-190">**Set using** : `CaptureStartupErrors`</span></span>  
<span data-ttu-id="d85f0-191">**환경 변수** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d85f0-191">**Environment variable** : `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d85f0-192">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d85f0-193">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="d85f0-194">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d85f0-194">Content root</span></span>

<span data-ttu-id="d85f0-195">이 설정은 ASP.NET Core가 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="d85f0-196">**키** : contentRoot</span><span class="sxs-lookup"><span data-stu-id="d85f0-196">**Key** : contentRoot</span></span>  
<span data-ttu-id="d85f0-197">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-197">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-198">**기본값** : 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-198">**Default** : Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d85f0-199">**설정 방법** : `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d85f0-199">**Set using** : `UseContentRoot`</span></span>  
<span data-ttu-id="d85f0-200">**환경 변수** : `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d85f0-200">**Environment variable** : `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="d85f0-201">콘텐츠 루트는 또한 [웹 루트](xref:fundamentals/index#web-root)에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="d85f0-202">콘텐츠 루트 경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="d85f0-203">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-203">For more information, see:</span></span>

* [<span data-ttu-id="d85f0-204">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d85f0-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d85f0-205">웹 루트</span><span class="sxs-lookup"><span data-stu-id="d85f0-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="d85f0-206">자세한 오류</span><span class="sxs-lookup"><span data-stu-id="d85f0-206">Detailed Errors</span></span>

<span data-ttu-id="d85f0-207">자세한 오류를 캡처해야 하는지를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="d85f0-208">**키** : detailedErrors</span><span class="sxs-lookup"><span data-stu-id="d85f0-208">**Key** : detailedErrors</span></span>  
<span data-ttu-id="d85f0-209">**형식** : *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="d85f0-209">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d85f0-210">**기본값** : false</span><span class="sxs-lookup"><span data-stu-id="d85f0-210">**Default** : false</span></span>  
<span data-ttu-id="d85f0-211">**설정 방법** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d85f0-211">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="d85f0-212">**환경 변수** : `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d85f0-212">**Environment variable** : `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="d85f0-213">사용하는 경우(또는 <a href="#environment">환경</a>이 `Development`로 설정된 경우) 앱은 자세한 예외를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="d85f0-214">환경</span><span class="sxs-lookup"><span data-stu-id="d85f0-214">Environment</span></span>

<span data-ttu-id="d85f0-215">앱의 환경을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-215">Sets the app's environment.</span></span>

<span data-ttu-id="d85f0-216">**키** : environment</span><span class="sxs-lookup"><span data-stu-id="d85f0-216">**Key** : environment</span></span>  
<span data-ttu-id="d85f0-217">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-217">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-218">**기본값** : Production</span><span class="sxs-lookup"><span data-stu-id="d85f0-218">**Default** : Production</span></span>  
<span data-ttu-id="d85f0-219">**설정 방법** : `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d85f0-219">**Set using** : `UseEnvironment`</span></span>  
<span data-ttu-id="d85f0-220">**환경 변수** : `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d85f0-220">**Environment variable** : `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="d85f0-221">환경은 어떠한 값으로도 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-221">The environment can be set to any value.</span></span> <span data-ttu-id="d85f0-222">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d85f0-223">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-223">Values aren't case sensitive.</span></span> <span data-ttu-id="d85f0-224">기본적으로 *환경* 은 `ASPNETCORE_ENVIRONMENT` 환경 변수에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="d85f0-225">[Visual Studio](https://visualstudio.microsoft.com)를 사용하는 경우 환경 변수는 *launchSettings.json* 파일에서 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="d85f0-226">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="d85f0-227">호스팅 시작 어셈블리</span><span class="sxs-lookup"><span data-stu-id="d85f0-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="d85f0-228">앱의 호스팅 시작 어셈블리를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="d85f0-229">**키** : hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d85f0-229">**Key** : hostingStartupAssemblies</span></span>  
<span data-ttu-id="d85f0-230">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-230">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-231">**기본값** : 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="d85f0-231">**Default** : Empty string</span></span>  
<span data-ttu-id="d85f0-232">**설정 방법** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d85f0-232">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="d85f0-233">**환경 변수** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d85f0-233">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d85f0-234">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="d85f0-235">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d85f0-236">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="d85f0-237">HTTPS 포트</span><span class="sxs-lookup"><span data-stu-id="d85f0-237">HTTPS Port</span></span>

<span data-ttu-id="d85f0-238">HTTPS 리디렉션 포트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="d85f0-239">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d85f0-240">**키** : https_port **형식** : *문자열*
**기본값** : 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-240">**Key** : https_port **Type** : *string*
**Default** : A default value isn't set.</span></span>
<span data-ttu-id="d85f0-241">**설정 방법** : `UseSetting`
**환경 변수** : `ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d85f0-241">**Set using** : `UseSetting`
**Environment variable** : `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="d85f0-242">호스팅 시작 제외 어셈블리</span><span class="sxs-lookup"><span data-stu-id="d85f0-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="d85f0-243">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d85f0-244">**키** : hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d85f0-244">**Key** : hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="d85f0-245">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-245">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-246">**기본값** : 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="d85f0-246">**Default** : Empty string</span></span>  
<span data-ttu-id="d85f0-247">**설정 방법** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d85f0-247">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="d85f0-248">**환경 변수** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d85f0-248">**Environment variable** : `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="d85f0-249">호스팅 URL 선호</span><span class="sxs-lookup"><span data-stu-id="d85f0-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="d85f0-250">호스트가 `IServer` 구현으로 구성된 URL 대신에 `WebHostBuilder`로 구성된 URL에서 수신 대기할지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d85f0-251">**키** : preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d85f0-251">**Key** : preferHostingUrls</span></span>  
<span data-ttu-id="d85f0-252">**형식** : *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="d85f0-252">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d85f0-253">**기본값** : true</span><span class="sxs-lookup"><span data-stu-id="d85f0-253">**Default** : true</span></span>  
<span data-ttu-id="d85f0-254">**설정 방법** : `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d85f0-254">**Set using** : `PreferHostingUrls`</span></span>  
<span data-ttu-id="d85f0-255">**환경 변수** : `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d85f0-255">**Environment variable** : `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="d85f0-256">호스팅 시작 방지</span><span class="sxs-lookup"><span data-stu-id="d85f0-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="d85f0-257">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d85f0-258">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d85f0-259">**키** : preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d85f0-259">**Key** : preventHostingStartup</span></span>  
<span data-ttu-id="d85f0-260">**형식** : *bool* (`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="d85f0-260">**Type** : *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="d85f0-261">**기본값** : false</span><span class="sxs-lookup"><span data-stu-id="d85f0-261">**Default** : false</span></span>  
<span data-ttu-id="d85f0-262">**설정 방법** : `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="d85f0-262">**Set using** : `UseSetting`</span></span>  
<span data-ttu-id="d85f0-263">**환경 변수** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d85f0-263">**Environment variable** : `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="d85f0-264">서버 URL</span><span class="sxs-lookup"><span data-stu-id="d85f0-264">Server URLs</span></span>

<span data-ttu-id="d85f0-265">서버에서 요청을 수신해야 하는 포트와 프로토콜이 있는 IP 주소 또는 호스트 주소를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="d85f0-266">**키** : urls</span><span class="sxs-lookup"><span data-stu-id="d85f0-266">**Key** : urls</span></span>  
<span data-ttu-id="d85f0-267">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-267">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-268">**기본** : http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="d85f0-268">**Default** : http://localhost:5000</span></span>  
<span data-ttu-id="d85f0-269">**설정 방법** : `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="d85f0-269">**Set using** : `UseUrls`</span></span>  
<span data-ttu-id="d85f0-270">**환경 변수** : `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="d85f0-270">**Environment variable** : `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="d85f0-271">서버가 응답해야 하는 세미콜론으로 구분된(;) URL 접두사의 목록으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="d85f0-272">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="d85f0-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d85f0-273">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="d85f0-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d85f0-274">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d85f0-275">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="d85f0-276">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d85f0-277">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d85f0-278">시스템 종료 제한 시간</span><span class="sxs-lookup"><span data-stu-id="d85f0-278">Shutdown Timeout</span></span>

<span data-ttu-id="d85f0-279">웹 호스트가 종료될 때까지 기다리는 시간을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="d85f0-280">**키** : shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="d85f0-280">**Key** : shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="d85f0-281">**형식** : *int*</span><span class="sxs-lookup"><span data-stu-id="d85f0-281">**Type** : *int*</span></span>  
<span data-ttu-id="d85f0-282">**기본값** : 5</span><span class="sxs-lookup"><span data-stu-id="d85f0-282">**Default** : 5</span></span>  
<span data-ttu-id="d85f0-283">**설정 방법** : `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="d85f0-283">**Set using** : `UseShutdownTimeout`</span></span>  
<span data-ttu-id="d85f0-284">**환경 변수** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d85f0-284">**Environment variable** : `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d85f0-285">키가 `UseSetting`을 사용할 때는 *int* 를 받지만(예: `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`) [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 확장 메서드는 [TimeSpan](/dotnet/api/system.timespan)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="d85f0-286">제한 시간 기간 동안 호스팅은:</span><span class="sxs-lookup"><span data-stu-id="d85f0-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="d85f0-287">[IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d85f0-288">중지하지 못한 서비스에 대한 모든 오류를 기록하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="d85f0-289">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d85f0-290">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d85f0-291">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="d85f0-292">시작 어셈블리</span><span class="sxs-lookup"><span data-stu-id="d85f0-292">Startup Assembly</span></span>

<span data-ttu-id="d85f0-293">`Startup` 클래스를 검색할 어셈블리를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d85f0-294">**키** : startupAssembly</span><span class="sxs-lookup"><span data-stu-id="d85f0-294">**Key** : startupAssembly</span></span>  
<span data-ttu-id="d85f0-295">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-295">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-296">**기본값** : 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="d85f0-296">**Default** : The app's assembly</span></span>  
<span data-ttu-id="d85f0-297">**설정 방법** : `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="d85f0-297">**Set using** : `UseStartup`</span></span>  
<span data-ttu-id="d85f0-298">**환경 변수** : `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d85f0-298">**Environment variable** : `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d85f0-299">이름(`string`) 또는 형식(`TStartup`)으로 어셈블리를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="d85f0-300">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="d85f0-301">웹 루트</span><span class="sxs-lookup"><span data-stu-id="d85f0-301">Web root</span></span>

<span data-ttu-id="d85f0-302">앱의 정적 자산에 대한 상대 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="d85f0-303">**키** : webroot</span><span class="sxs-lookup"><span data-stu-id="d85f0-303">**Key** : webroot</span></span>  
<span data-ttu-id="d85f0-304">**형식** : *string*</span><span class="sxs-lookup"><span data-stu-id="d85f0-304">**Type** : *string*</span></span>  
<span data-ttu-id="d85f0-305">**기본값** : 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-305">**Default** : The default is `wwwroot`.</span></span> <span data-ttu-id="d85f0-306">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="d85f0-307">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="d85f0-308">**설정 방법** : `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="d85f0-308">**Set using** : `UseWebRoot`</span></span>  
<span data-ttu-id="d85f0-309">**환경 변수** : `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d85f0-309">**Environment variable** : `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="d85f0-310">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-310">For more information, see:</span></span>

* [<span data-ttu-id="d85f0-311">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="d85f0-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d85f0-312">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d85f0-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="d85f0-313">구성 재정의</span><span class="sxs-lookup"><span data-stu-id="d85f0-313">Override configuration</span></span>

<span data-ttu-id="d85f0-314">[구성](xref:fundamentals/configuration/index)을 사용하여 웹 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="d85f0-315">다음 예제에서는 호스트 구성이 필요에 따라 *hostsettings.json* 파일에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="d85f0-316">*hostsettings.json* 파일에서 로드된 모든 구성은 명령줄 인수에 의해 재정의될 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="d85f0-317">빌드된 구성(`config`에 있음)은 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)을 통해 호스트를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="d85f0-318">`IWebHostBuilder` 구성이 앱의 구성에 추가되지만 반대의 경우에는 true가 아닙니다. &mdash;`ConfigureAppConfiguration`은 `IWebHostBuilder` 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="d85f0-319">`UseUrls`에 의해 제공되는 구성을 먼저 *hostsettings.json* 의 구성으로 재정의하고, 두 번째로 명령줄 인수의 구성으로 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="d85f0-320">*hostsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="d85f0-320">*hostsettings.json* :</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="d85f0-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)은 제공된 `IConfiguration`의 키만 호스트 작성기 구성에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="d85f0-322">따라서 JSON, INI 및 XML 설정 파일에 대해 `reloadOnChange: true`를 설정해도 영향을 미치지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="d85f0-323">특정 URL에서 실행하는 호스트를 지정하려면 [dotnet run](/dotnet/core/tools/dotnet-run) 실행 시 원하는 값을 명령 프롬프트에서 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="d85f0-324">명령줄 인수는 *hostsettings.json* 파일의 `urls` 값을 재정의하고, 서버는 포트 8080에서 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="d85f0-325">호스트 관리</span><span class="sxs-lookup"><span data-stu-id="d85f0-325">Manage the host</span></span>

<span data-ttu-id="d85f0-326">**Run**</span><span class="sxs-lookup"><span data-stu-id="d85f0-326">**Run**</span></span>

<span data-ttu-id="d85f0-327">`Run` 메서드는 웹앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="d85f0-328">**Start**</span><span class="sxs-lookup"><span data-stu-id="d85f0-328">**Start**</span></span>

<span data-ttu-id="d85f0-329">해당 `Start` 메서드를 호출하여 비차단 방식으로 호스트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="d85f0-330">URL 목록이 `Start` 메서드에 전달되면 지정된 URL에서 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="d85f0-331">앱은 정적 편의 메서드를 사용하여 `CreateDefaultBuilder`의 미리 구성된 기본 값을 사용하는 새 호스트를 초기화하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="d85f0-332">이러한 메서드는 콘솔 출력 없이 서버를 시작하고 [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown)으로 중단(Ctrl-C/SIGINT 또는 SIGTERM)될 때까지 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="d85f0-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="d85f0-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="d85f0-334">`RequestDelegate`로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d85f0-335">`http://localhost:5000`에 대한 브라우저에서 요청을 수행하여 “Hello World!” 응답을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="d85f0-336">`WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="d85f0-337">앱은 `Console.WriteLine` 메시지를 표시하고 종료하기 위한 키 입력을 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="d85f0-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="d85f0-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="d85f0-339">URL 및 `RequestDelegate`로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d85f0-340">앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **Start(RequestDelegate app)** 와 동일한 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-340">Produces the same result as **Start(RequestDelegate app)** , except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="d85f0-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="d85f0-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="d85f0-342">`IRouteBuilder`의 인스턴스([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/))를 사용하여 라우팅 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d85f0-343">예제에서는 다음 브라우저 요청을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="d85f0-344">요청</span><span class="sxs-lookup"><span data-stu-id="d85f0-344">Request</span></span>                                    | <span data-ttu-id="d85f0-345">응답</span><span class="sxs-lookup"><span data-stu-id="d85f0-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="d85f0-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="d85f0-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="d85f0-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="d85f0-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="d85f0-348">“ooops!” 문자열을 사용하여 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="d85f0-349">“Uh oh!” 문자열을 사용하여 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="d85f0-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="d85f0-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="d85f0-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="d85f0-351">Hello World!</span></span>                             |

<span data-ttu-id="d85f0-352">`WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="d85f0-353">앱은 `Console.WriteLine` 메시지를 표시하고 keypress가 종료될 때까지 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="d85f0-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="d85f0-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="d85f0-355">URL 및 `IRouteBuilder`의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d85f0-356">앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **Start(Action\<IRouteBuilder> routeBuilder)** 와 동일한 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)** , except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="d85f0-357">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="d85f0-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="d85f0-358">대리자를 제공하여 `IApplicationBuilder`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d85f0-359">`http://localhost:5000`에 대한 브라우저에서 요청을 수행하여 “Hello World!” 응답을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="d85f0-360">`WaitForShutdown`은 중단(Ctrl-C/SIGINT 또는 SIGTERM)이 발생할 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="d85f0-361">앱은 `Console.WriteLine` 메시지를 표시하고 종료하기 위한 키 입력을 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="d85f0-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="d85f0-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="d85f0-363">URL 및 대리자를 제공하여 `IApplicationBuilder`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="d85f0-364">앱이 `http://localhost:8080`에서 응답한다는 점을 제외하고 **StartWith(Action\<IApplicationBuilder> app)** 와 동일한 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)** , except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="d85f0-365">IWebHostEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="d85f0-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="d85f0-366">`IWebHostEnvironment` 인터페이스는 앱의 웹 호스팅 환경에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="d85f0-367">해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 `IWebHostEnvironment`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="d85f0-368">[규칙 기반 접근 방식](xref:fundamentals/environments#environment-based-startup-class-and-methods)은 시작할 때 환경에 따라 앱을 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="d85f0-369">또는 `ConfigureServices`에서 사용할 수 있도록 `IWebHostEnvironment`를 `Startup` 생성자에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="d85f0-370">`IsDevelopment` 확장 메서드 외에 `IWebHostEnvironment`는 `IsStaging`, `IsProduction` 및 `IsEnvironment(string environmentName)` 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="d85f0-371">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d85f0-372">또한 `IWebHostEnvironment` 서비스를 파이프라인 처리를 설정하기 위한 `Configure` 메서드에 직접 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="d85f0-373">사용자 지정 [미들웨어](xref:fundamentals/middleware/write)를 만들 때 `IWebHostEnvironment`를 `Invoke` 메서드에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d85f0-374">IHostingEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="d85f0-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="d85f0-375">[IHostingEnvironment 인터페이스](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)는 앱의 웹 호스팅 환경에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="d85f0-376">해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 `IHostingEnvironment`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="d85f0-377">[규칙 기반 접근 방식](xref:fundamentals/environments#environment-based-startup-class-and-methods)은 시작할 때 환경에 따라 앱을 구성하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="d85f0-378">또는 `ConfigureServices`에서 사용할 수 있도록 `IHostingEnvironment`를 `Startup` 생성자에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="d85f0-379">`IsDevelopment` 확장 메서드 외에 `IHostingEnvironment`는 `IsStaging`, `IsProduction` 및 `IsEnvironment(string environmentName)` 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="d85f0-380">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d85f0-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d85f0-381">또한 `IHostingEnvironment` 서비스를 파이프라인 처리를 설정하기 위한 `Configure` 메서드에 직접 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="d85f0-382">사용자 지정 [미들웨어](xref:fundamentals/middleware/write)를 만들 때 `IHostingEnvironment`를 `Invoke` 메서드에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="d85f0-383">IHostApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="d85f0-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="d85f0-384">`IHostApplicationLifetime`은 시작 후 및 종료 작업을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="d85f0-385">인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 `Action` 메서드를 등록하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d85f0-386">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="d85f0-386">Cancellation Token</span></span>    | <span data-ttu-id="d85f0-387">트리거되는 경우:</span><span class="sxs-lookup"><span data-stu-id="d85f0-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="d85f0-388">호스트가 완벽하게 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="d85f0-389">호스트가 정상적으로 종료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d85f0-390">모든 요청이 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-390">All requests should be processed.</span></span> <span data-ttu-id="d85f0-391">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="d85f0-392">호스트가 정상적으로 종료되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d85f0-393">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-393">Requests may still be processing.</span></span> <span data-ttu-id="d85f0-394">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="d85f0-395">`StopApplication`은 앱의 종료를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="d85f0-396">다음 클래스에서는 `StopApplication`을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d85f0-397">IApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="d85f0-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="d85f0-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime)은 사후 시작 및 종료 작업을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="d85f0-399">인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 `Action` 메서드를 등록하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d85f0-400">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="d85f0-400">Cancellation Token</span></span>    | <span data-ttu-id="d85f0-401">트리거되는 경우:</span><span class="sxs-lookup"><span data-stu-id="d85f0-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="d85f0-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="d85f0-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="d85f0-403">호스트가 완벽하게 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-403">The host has fully started.</span></span> |
| [<span data-ttu-id="d85f0-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="d85f0-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="d85f0-405">호스트가 정상적으로 종료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d85f0-406">모든 요청이 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-406">All requests should be processed.</span></span> <span data-ttu-id="d85f0-407">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="d85f0-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="d85f0-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="d85f0-409">호스트가 정상적으로 종료되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d85f0-410">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-410">Requests may still be processing.</span></span> <span data-ttu-id="d85f0-411">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="d85f0-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication)은 앱의 종료를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="d85f0-413">다음 클래스에서는 `StopApplication`을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="d85f0-414">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="d85f0-414">Scope validation</span></span>

<span data-ttu-id="d85f0-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder)는 앱의 환경이 개발인 경우 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes)를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="d85f0-416">`ValidateScopes`이 `true`로 설정된 경우 기본 서비스 공급자는 다음을 확인하기 위해 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="d85f0-417">범위가 지정된 서비스는 직접 또는 간접적으로 루트 서비스 공급자에서 해결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="d85f0-418">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="d85f0-419">루트 서비스 공급자는 [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider)를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="d85f0-420">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="d85f0-421">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="d85f0-422">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="d85f0-423">서비스 범위의 유효성 검사는 `BuildServiceProvider`이 호출될 경우 이러한 상황을 알아챕니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="d85f0-424">프로덕션 환경을 포함하여 범위의 유효성을 검사하려면 호스트 작성기에서 [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions)을 [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider)로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d85f0-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="d85f0-425">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d85f0-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
