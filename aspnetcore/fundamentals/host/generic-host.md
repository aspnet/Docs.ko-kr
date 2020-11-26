---
title: ASP.NET Core의 .NET 일반 호스트
author: rick-anderson
description: ASP.NET Core 앱에서 .NET Core 일반 호스트를 사용합니다.  일반 호스트는 앱 시작 및 수명 관리를 담당합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: 263c7713166005dfdec8ede6bfa9b03b730dede7
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035816"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="119b7-104">ASP.NET Core의 .NET 일반 호스트</span><span class="sxs-lookup"><span data-stu-id="119b7-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="119b7-105">ASP.NET Core 템플릿은 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="119b7-106">이 항목에서는 ASP.NET Core에서 .NET 일반 호스트를 사용하는 방법에 관한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="119b7-107">콘솔 앱에서 .NET 일반 호스트를 사용하는 방법에 관한 자세한 내용은 [.NET 일반 호스트](/dotnet/core/extensions/generic-host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="119b7-108">호스트 정의</span><span class="sxs-lookup"><span data-stu-id="119b7-108">Host definition</span></span>

<span data-ttu-id="119b7-109">*호스트* 는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="119b7-110">DI(종속성 주입)</span><span class="sxs-lookup"><span data-stu-id="119b7-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="119b7-111">로깅</span><span class="sxs-lookup"><span data-stu-id="119b7-111">Logging</span></span>
* <span data-ttu-id="119b7-112">Configuration</span><span class="sxs-lookup"><span data-stu-id="119b7-112">Configuration</span></span>
* <span data-ttu-id="119b7-113">`IHostedService` 구현</span><span class="sxs-lookup"><span data-stu-id="119b7-113">`IHostedService` implementations</span></span>

<span data-ttu-id="119b7-114">호스트가 시작될 때 서비스 컨테이너의 호스티드 서비스 컬렉션에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현에서 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="119b7-115">웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="119b7-116">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="119b7-117">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-117">Set up a host</span></span>

<span data-ttu-id="119b7-118">호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="119b7-119">`Main` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-119">The `Main` method:</span></span>

* <span data-ttu-id="119b7-120">`CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="119b7-121">작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="119b7-122">ASP.NET Core 웹 템플릿은 다음과 같은 코드를 생성하여 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
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
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="119b7-123">다음 코드는 DI 컨테이너에 `IHostedService` 구현이 추가된 비 HTTP 워크로드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="119b7-124">HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="119b7-125">앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="119b7-126">[Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="119b7-127">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="119b7-128">기본 작성기 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-128">Default builder settings</span></span>

<span data-ttu-id="119b7-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="119b7-130">[콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory*>에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="119b7-131">다음에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="119b7-132">접두사가 `DOTNET_`인 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="119b7-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="119b7-133">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="119b7-133">Command-line arguments.</span></span>
* <span data-ttu-id="119b7-134">다음에서 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="119b7-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="119b7-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="119b7-136">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="119b7-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="119b7-137">[사용자 비밀](xref:security/app-secrets) - 앱이 `Development` 환경에서 실행되는 경우.</span><span class="sxs-lookup"><span data-stu-id="119b7-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="119b7-138">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="119b7-138">Environment variables.</span></span>
  * <span data-ttu-id="119b7-139">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="119b7-139">Command-line arguments.</span></span>
* <span data-ttu-id="119b7-140">다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="119b7-141">Console</span><span class="sxs-lookup"><span data-stu-id="119b7-141">Console</span></span>
  * <span data-ttu-id="119b7-142">Debug</span><span class="sxs-lookup"><span data-stu-id="119b7-142">Debug</span></span>
  * <span data-ttu-id="119b7-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="119b7-143">EventSource</span></span>
  * <span data-ttu-id="119b7-144">EventLog(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="119b7-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="119b7-145">환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="119b7-146"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="119b7-147">접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="119b7-148">[Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="119b7-149">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="119b7-150">[호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="119b7-151">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="119b7-152">IIS 통합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-152">Enables IIS integration.</span></span> <span data-ttu-id="119b7-153">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="119b7-154">이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="119b7-155">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="119b7-155">Framework-provided services</span></span>

<span data-ttu-id="119b7-156">다음 서비스가 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="119b7-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="119b7-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="119b7-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="119b7-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="119b7-160">프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="119b7-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="119b7-162"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="119b7-163">인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="119b7-164">인터페이스에는 `StopApplication` 메서드도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="119b7-165">다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="119b7-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-166">IHostLifetime</span></span>

<span data-ttu-id="119b7-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="119b7-168">등록된 마지막 구현이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-168">The last implementation registered is used.</span></span>

<span data-ttu-id="119b7-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="119b7-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="119b7-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="119b7-171"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="119b7-172">[RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="119b7-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="119b7-173">IHostEnvironment</span></span>

<span data-ttu-id="119b7-174"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="119b7-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="119b7-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="119b7-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="119b7-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="119b7-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="119b7-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="119b7-178">웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="119b7-179">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="119b7-179">Host configuration</span></span>

<span data-ttu-id="119b7-180">호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="119b7-181">호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="119b7-182">`ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="119b7-183">호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="119b7-184">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="119b7-185">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="119b7-186">접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="119b7-187">웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="119b7-188">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="119b7-189">예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="119b7-190">다음 예제에서는 호스트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="119b7-191">앱 구성</span><span class="sxs-lookup"><span data-stu-id="119b7-191">App configuration</span></span>

<span data-ttu-id="119b7-192">앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="119b7-193">`ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="119b7-194">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="119b7-195">`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="119b7-196">호스트 구성도 앱 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="119b7-197">자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="119b7-198">모든 앱 유형에 대한 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-198">Settings for all app types</span></span>

<span data-ttu-id="119b7-199">이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="119b7-200">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="119b7-201">자세한 내용은 [기본 작성기 설정](#default-builder-settings) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="119b7-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="119b7-202">ApplicationName</span></span>

<span data-ttu-id="119b7-203">호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="119b7-204">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="119b7-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="119b7-205">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-205">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-206">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="119b7-207">**환경 변수**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="119b7-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="119b7-208">이 값을 설정하려면 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="119b7-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-209">ContentRoot</span></span>

<span data-ttu-id="119b7-210">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="119b7-211">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="119b7-212">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="119b7-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="119b7-213">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-213">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-214">**기본값**: 앱 어셈블리가 있는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="119b7-215">**환경 변수**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="119b7-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="119b7-216">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="119b7-217">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-217">For more information, see:</span></span>

* [<span data-ttu-id="119b7-218">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="119b7-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="119b7-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="119b7-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="119b7-220">EnvironmentName</span></span>

<span data-ttu-id="119b7-221">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="119b7-222">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="119b7-223">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="119b7-224">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="119b7-224">**Key**: `environment`</span></span>  
<span data-ttu-id="119b7-225">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-225">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-226">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="119b7-226">**Default**: `Production`</span></span>  
<span data-ttu-id="119b7-227">**환경 변수**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="119b7-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="119b7-228">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="119b7-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="119b7-229">ShutdownTimeout</span></span>

<span data-ttu-id="119b7-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="119b7-231">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-231">The default value is five seconds.</span></span>  <span data-ttu-id="119b7-232">시간 제한 기간 동안 호스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="119b7-233">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="119b7-234">중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="119b7-235">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="119b7-236">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="119b7-237">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="119b7-238">**키**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="119b7-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="119b7-239">**형식**: `int`</span><span class="sxs-lookup"><span data-stu-id="119b7-239">**Type**: `int`</span></span>  
<span data-ttu-id="119b7-240">**기본값**: 5초</span><span class="sxs-lookup"><span data-stu-id="119b7-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="119b7-241">**환경 변수**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="119b7-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="119b7-242">이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="119b7-243">다음 예제에서는 시간 제한을 20초로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="119b7-244">변경 시 앱 구성 다시 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="119b7-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="119b7-245">[기본적으로](xref:fundamentals/configuration/index#default) 파일이 변경되면 *appsettings.json* 및 *appsettings.{Environment}.json* 이 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="119b7-246">ASP.NET Core 5.0 이상에서 해당 다시 로드 동작을 사용하지 않도록 설정하려면 `hostBuilder:reloadConfigOnChange` 키를 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="119b7-247">**키**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="119b7-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="119b7-248">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-249">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="119b7-249">**Default**: `true`</span></span>  
<span data-ttu-id="119b7-250">**명령줄 인수**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="119b7-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="119b7-251">**환경 변수**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="119b7-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="119b7-252">콜론(`:`) 구분 기호는 모든 플랫폼의 환경 변수 계층적 키에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="119b7-253">자세한 내용은 [환경 변수](xref:fundamentals/configuration/index#environment-variables)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="119b7-254">웹앱 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-254">Settings for web apps</span></span>

<span data-ttu-id="119b7-255">일부 호스트 설정은 HTTP 워크로드에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="119b7-256">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="119b7-257">이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="119b7-258">확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="119b7-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="119b7-259">CaptureStartupErrors</span></span>

<span data-ttu-id="119b7-260">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="119b7-261">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="119b7-262">**키**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="119b7-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="119b7-263">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-264">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="119b7-265">**환경 변수**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="119b7-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="119b7-266">이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="119b7-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="119b7-267">DetailedErrors</span></span>

<span data-ttu-id="119b7-268">활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="119b7-269">**키**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="119b7-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="119b7-270">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-271">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="119b7-271">**Default**: `false`</span></span>  
<span data-ttu-id="119b7-272">**환경 변수**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="119b7-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="119b7-273">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="119b7-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="119b7-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="119b7-275">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="119b7-276">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="119b7-277">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="119b7-278">**키**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="119b7-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="119b7-279">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-279">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-280">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="119b7-280">**Default**: Empty string</span></span>  
<span data-ttu-id="119b7-281">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="119b7-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="119b7-282">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="119b7-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="119b7-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="119b7-284">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="119b7-285">**키**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="119b7-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="119b7-286">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-286">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-287">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="119b7-287">**Default**: Empty string</span></span>  
<span data-ttu-id="119b7-288">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="119b7-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="119b7-289">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="119b7-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="119b7-290">HTTPS_Port</span></span>

<span data-ttu-id="119b7-291">HTTPS 리디렉션 포트.</span><span class="sxs-lookup"><span data-stu-id="119b7-291">The HTTPS redirect port.</span></span> <span data-ttu-id="119b7-292">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="119b7-293">**키**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="119b7-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="119b7-294">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-294">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-295">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="119b7-296">**환경 변수**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="119b7-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="119b7-297">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="119b7-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="119b7-298">PreferHostingUrls</span></span>

<span data-ttu-id="119b7-299">호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="119b7-300">**키**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="119b7-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="119b7-301">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-302">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="119b7-302">**Default**: `true`</span></span>  
<span data-ttu-id="119b7-303">**환경 변수**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="119b7-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="119b7-304">이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="119b7-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="119b7-305">PreventHostingStartup</span></span>

<span data-ttu-id="119b7-306">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="119b7-307">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="119b7-308">**키**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="119b7-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="119b7-309">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-310">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="119b7-310">**Default**: `false`</span></span>  
<span data-ttu-id="119b7-311">**환경 변수**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="119b7-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="119b7-312">이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="119b7-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="119b7-313">StartupAssembly</span></span>

<span data-ttu-id="119b7-314">`Startup` 클래스를 검색할 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="119b7-315">**키**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="119b7-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="119b7-316">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-316">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-317">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="119b7-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="119b7-318">**환경 변수**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="119b7-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="119b7-319">이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="119b7-320">`UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="119b7-321">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="119b7-322">URL</span><span class="sxs-lookup"><span data-stu-id="119b7-322">URLs</span></span>

<span data-ttu-id="119b7-323">서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="119b7-324">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="119b7-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="119b7-325">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="119b7-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="119b7-326">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="119b7-327">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="119b7-328">**키**: `urls`</span><span class="sxs-lookup"><span data-stu-id="119b7-328">**Key**: `urls`</span></span>  
<span data-ttu-id="119b7-329">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-329">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-330">**기본값**: `http://localhost:5000` 및 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="119b7-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="119b7-331">**환경 변수**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="119b7-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="119b7-332">이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="119b7-333">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="119b7-334">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-334">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="119b7-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-335">WebRoot</span></span>

<span data-ttu-id="119b7-336">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 속성은 앱 정적 자산의 상대 경로를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="119b7-337">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="119b7-338">**키**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="119b7-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="119b7-339">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-339">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-340">**기본값**: 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="119b7-341">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="119b7-342">**환경 변수**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="119b7-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="119b7-343">이 값을 설정하려면 환경 변수를 사용하거나 `IWebHostBuilder`에서 `UseWebRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="119b7-344">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-344">For more information, see:</span></span>

* [<span data-ttu-id="119b7-345">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="119b7-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="119b7-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="119b7-347">호스트 수명 관리</span><span class="sxs-lookup"><span data-stu-id="119b7-347">Manage the host lifetime</span></span>

<span data-ttu-id="119b7-348">기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="119b7-349">이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="119b7-350">Run</span><span class="sxs-lookup"><span data-stu-id="119b7-350">Run</span></span>

<span data-ttu-id="119b7-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="119b7-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-352">RunAsync</span></span>

<span data-ttu-id="119b7-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="119b7-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-354">RunConsoleAsync</span></span>

<span data-ttu-id="119b7-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="119b7-356">Start</span><span class="sxs-lookup"><span data-stu-id="119b7-356">Start</span></span>

<span data-ttu-id="119b7-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="119b7-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-358">StartAsync</span></span>

<span data-ttu-id="119b7-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="119b7-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="119b7-361">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="119b7-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-362">StopAsync</span></span>

<span data-ttu-id="119b7-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="119b7-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="119b7-364">WaitForShutdown</span></span>

<span data-ttu-id="119b7-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="119b7-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="119b7-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="119b7-368">외부 제어</span><span class="sxs-lookup"><span data-stu-id="119b7-368">External control</span></span>

<span data-ttu-id="119b7-369">호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="119b7-370">ASP.NET Core 템플릿은 .NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="119b7-371">이 항목에서는 ASP.NET Core에서 .NET 일반 호스트를 사용하는 방법에 관한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="119b7-372">콘솔 앱에서 .NET 일반 호스트를 사용하는 방법에 관한 자세한 내용은 [.NET 일반 호스트](/dotnet/core/extensions/generic-host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="119b7-373">호스트 정의</span><span class="sxs-lookup"><span data-stu-id="119b7-373">Host definition</span></span>

<span data-ttu-id="119b7-374">*호스트* 는 다음과 같이 앱의 리소스를 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="119b7-375">DI(종속성 주입)</span><span class="sxs-lookup"><span data-stu-id="119b7-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="119b7-376">로깅</span><span class="sxs-lookup"><span data-stu-id="119b7-376">Logging</span></span>
* <span data-ttu-id="119b7-377">Configuration</span><span class="sxs-lookup"><span data-stu-id="119b7-377">Configuration</span></span>
* <span data-ttu-id="119b7-378">`IHostedService` 구현</span><span class="sxs-lookup"><span data-stu-id="119b7-378">`IHostedService` implementations</span></span>

<span data-ttu-id="119b7-379">호스트가 시작될 때 서비스 컨테이너의 호스티드 서비스 컬렉션에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService>의 각 구현에서 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="119b7-380">웹앱에서 `IHostedService` 구현 중 하나는 [HTTP 서버 구현](xref:fundamentals/index#servers)을 시작하는 웹 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="119b7-381">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="119b7-382">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-382">Set up a host</span></span>

<span data-ttu-id="119b7-383">호스트는 일반적으로 `Program` 클래스의 코드로 구성, 빌드 및 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="119b7-384">`Main` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-384">The `Main` method:</span></span>

* <span data-ttu-id="119b7-385">`CreateHostBuilder` 메서드를 호출하여 작성기 개체를 만들고 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="119b7-386">작성기 개체에서 `Build` 및 `Run` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="119b7-387">ASP.NET Core 웹 템플릿은 다음과 같은 코드를 생성하여 일반 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
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
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="119b7-388">다음 코드는 비 HTTP 워크로드를 사용하여 일반 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="119b7-389">`IHostedService` 구현은 DI 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-389">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="119b7-390">HTTP 워크로드의 경우 `Main` 메서드는 동일하지만 `CreateHostBuilder`는 `ConfigureWebHostDefaults`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="119b7-391">위의 코드는 ASP.NET Core 템플릿에서 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="119b7-392">앱에서 Entity Framework Core를 사용하는 경우 `CreateHostBuilder` 메서드의 이름이나 서명을 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="119b7-393">[Entity Framework Core 도구](/ef/core/miscellaneous/cli/)는 앱을 실행하지 않고 호스트를 구성하는 `CreateHostBuilder` 메서드를 찾으려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="119b7-394">자세한 내용은 [디자인 타임 DbContext 만들기](/ef/core/miscellaneous/cli/dbcontext-creation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="119b7-395">기본 작성기 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-395">Default builder settings</span></span>

<span data-ttu-id="119b7-396"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="119b7-397">[콘텐츠 루트](xref:fundamentals/index#content-root)를 <xref:System.IO.Directory.GetCurrentDirectory%2A>에서 반환된 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="119b7-398">다음에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="119b7-399">접두사가 `DOTNET_`인 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="119b7-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="119b7-400">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="119b7-400">Command-line arguments.</span></span>
* <span data-ttu-id="119b7-401">다음에서 앱 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="119b7-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="119b7-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="119b7-403">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="119b7-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="119b7-404">[사용자 비밀](xref:security/app-secrets) - 앱이 `Development` 환경에서 실행되는 경우.</span><span class="sxs-lookup"><span data-stu-id="119b7-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="119b7-405">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="119b7-405">Environment variables.</span></span>
  * <span data-ttu-id="119b7-406">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="119b7-406">Command-line arguments.</span></span>
* <span data-ttu-id="119b7-407">다음 [로깅](xref:fundamentals/logging/index) 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="119b7-408">Console</span><span class="sxs-lookup"><span data-stu-id="119b7-408">Console</span></span>
  * <span data-ttu-id="119b7-409">Debug</span><span class="sxs-lookup"><span data-stu-id="119b7-409">Debug</span></span>
  * <span data-ttu-id="119b7-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="119b7-410">EventSource</span></span>
  * <span data-ttu-id="119b7-411">EventLog(Windows에서 실행 중인 경우에만)</span><span class="sxs-lookup"><span data-stu-id="119b7-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="119b7-412">환경이 개발 중일 때 [범위 유효성 검사](xref:fundamentals/dependency-injection#scope-validation) 및 [종속성 유효성 검사](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="119b7-413">`ConfigureWebHostDefaults` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="119b7-414">접두사가 `ASPNETCORE_`인 환경 변수에서 호스트 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="119b7-415">[Kestrel](xref:fundamentals/servers/kestrel) 서버를 웹 서버로 설정하고 앱의 호스팅 구성 공급자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="119b7-416">Kestrel 서버의 기본 옵션은 <xref:fundamentals/servers/kestrel#kestrel-options>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="119b7-417">[호스트 필터링 미들웨어](xref:fundamentals/servers/kestrel#host-filtering)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="119b7-418">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`가 `true`와 같으면 [전달된 헤더 미들웨어](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="119b7-419">IIS 통합을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-419">Enables IIS integration.</span></span> <span data-ttu-id="119b7-420">IIS 기본 옵션은 <xref:host-and-deploy/iis/index#iis-options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="119b7-421">이 문서 뒷부분의 [모든 앱 유형에 대한 설정](#settings-for-all-app-types) 및 [웹앱 설정](#settings-for-web-apps) 섹션에서는 기본 작성기 설정을 재정의하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="119b7-422">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="119b7-422">Framework-provided services</span></span>

<span data-ttu-id="119b7-423">다음 서비스가 자동으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="119b7-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="119b7-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="119b7-426">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="119b7-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="119b7-427">프레임워크에서 제공하는 서비스에 대한 자세한 내용은 <xref:fundamentals/dependency-injection#framework-provided-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="119b7-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="119b7-429"><xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>(이전의 `IApplicationLifetime`) 서비스를 모든 클래스에 삽입하여 사후 시작 및 정상 종료 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="119b7-430">인터페이스의 세 가지 속성은 앱 시작 및 앱 중지 이벤트 처리기 메서드를 등록하는 데 사용되는 취소 토큰입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="119b7-431">인터페이스에는 `StopApplication` 메서드도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="119b7-432">다음 예제는 `IHostApplicationLifetime` 이벤트를 등록하는 `IHostedService` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="119b7-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-433">IHostLifetime</span></span>

<span data-ttu-id="119b7-434"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 구현은 호스트가 시작될 때와 중지될 때를 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="119b7-435">등록된 마지막 구현이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-435">The last implementation registered is used.</span></span>

<span data-ttu-id="119b7-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 `IHostLifetime` 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="119b7-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="119b7-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="119b7-438"><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="119b7-439">[RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="119b7-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="119b7-440">IHostEnvironment</span></span>

<span data-ttu-id="119b7-441"><xref:Microsoft.Extensions.Hosting.IHostEnvironment> 서비스를 클래스에 삽입하여 다음 설정에 대한 정보를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="119b7-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="119b7-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="119b7-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="119b7-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="119b7-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="119b7-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="119b7-445">웹앱은 `IHostEnvironment`를 상속하고 [WebRootPath](#webroot)를 추가하는 `IWebHostEnvironment` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="119b7-446">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="119b7-446">Host configuration</span></span>

<span data-ttu-id="119b7-447">호스트 구성은 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 구현의 속성에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="119b7-448">호스트 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 내부에 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration)에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="119b7-449">`ConfigureAppConfiguration` 다음에 `HostBuilderContext.Configuration`이 앱 구성으로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="119b7-450">호스트 구성을 추가하려면 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="119b7-451">`ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="119b7-452">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="119b7-453">접두사 `DOTNET_` 및 명령줄 인수가 있는 환경 변수 공급자는 `CreateDefaultBuilder`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="119b7-454">웹앱의 경우 접두사 `ASPNETCORE_`가 있는 환경 변수 공급자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="119b7-455">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="119b7-456">예를 들어 `ASPNETCORE_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="119b7-457">다음 예제에서는 호스트 구성을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="119b7-458">앱 구성</span><span class="sxs-lookup"><span data-stu-id="119b7-458">App configuration</span></span>

<span data-ttu-id="119b7-459">앱 구성은 `IHostBuilder`에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="119b7-460">`ConfigureAppConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="119b7-461">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="119b7-462">`ConfigureAppConfiguration`에 의해 생성된 구성은 다음 작업을 위해 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*)에서 사용 가능하거나 DI의 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="119b7-463">호스트 구성도 앱 구성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="119b7-464">자세한 내용은 [ASP.NET Core의 구성](xref:fundamentals/configuration/index#configureappconfiguration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="119b7-465">모든 앱 유형에 대한 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-465">Settings for all app types</span></span>

<span data-ttu-id="119b7-466">이 섹션에는 HTTP 및 비 HTTP 워크로드 모두에 적용되는 호스트 설정이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="119b7-467">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="119b7-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="119b7-468">ApplicationName</span></span>

<span data-ttu-id="119b7-469">호스트를 생성하는 동안 호스트 구성에서 [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="119b7-470">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="119b7-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="119b7-471">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-471">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-472">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="119b7-473">**환경 변수**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="119b7-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="119b7-474">이 값을 설정하려면 환경 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="119b7-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-475">ContentRoot</span></span>

<span data-ttu-id="119b7-476">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 속성은 콘텐츠 파일 검색을 시작하는 위치를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="119b7-477">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="119b7-478">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="119b7-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="119b7-479">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-479">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-480">**기본값**: 앱 어셈블리가 있는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="119b7-481">**환경 변수**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="119b7-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="119b7-482">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseContentRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="119b7-483">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-483">For more information, see:</span></span>

* [<span data-ttu-id="119b7-484">기본 사항: 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="119b7-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="119b7-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="119b7-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="119b7-486">EnvironmentName</span></span>

<span data-ttu-id="119b7-487">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 속성을 임의의 값으로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="119b7-488">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="119b7-489">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="119b7-490">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="119b7-490">**Key**: `environment`</span></span>  
<span data-ttu-id="119b7-491">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-491">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-492">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="119b7-492">**Default**: `Production`</span></span>  
<span data-ttu-id="119b7-493">**환경 변수**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="119b7-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="119b7-494">이 값을 설정하려면 환경 변수를 사용하거나 `IHostBuilder`에서 `UseEnvironment`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="119b7-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="119b7-495">ShutdownTimeout</span></span>

<span data-ttu-id="119b7-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*)이 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="119b7-497">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-497">The default value is five seconds.</span></span>  <span data-ttu-id="119b7-498">시간 제한 기간 동안 호스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="119b7-499">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)을 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="119b7-500">중지하지 못한 서비스에 대한 오류를 로깅하면서 호스팅된 서비스 중지를 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="119b7-501">모든 호스팅된 서비스가 중지하기 전에 시간 제한 기간이 만료되면 앱이 종료될 때 모든 활성화된 나머지 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="119b7-502">처리를 완료하지 않은 경우에도 서비스가 중지됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="119b7-503">서비스를 중지하는 데 시간이 더 필요한 경우 시간 제한을 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="119b7-504">**키**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="119b7-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="119b7-505">**형식**: `int`</span><span class="sxs-lookup"><span data-stu-id="119b7-505">**Type**: `int`</span></span>  
<span data-ttu-id="119b7-506">**기본값**: 5초</span><span class="sxs-lookup"><span data-stu-id="119b7-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="119b7-507">**환경 변수**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="119b7-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="119b7-508">이 값을 설정하려면 환경 변수를 사용하거나 `HostOptions`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="119b7-509">다음 예제에서는 시간 제한을 20초로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="119b7-510">웹앱 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-510">Settings for web apps</span></span>

<span data-ttu-id="119b7-511">일부 호스트 설정은 HTTP 워크로드에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="119b7-512">기본적으로 이러한 설정을 구성하는 데 사용되는 환경 변수에는 `DOTNET_` 또는 `ASPNETCORE_` 접두사가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="119b7-513">이러한 설정에 대해 `IWebHostBuilder`의 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="119b7-514">확장 메서드를 호출하는 방법을 보여주는 코드 샘플은 다음 예제와 같이 `webBuilder`가 `IWebHostBuilder`의 인스턴스라고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="119b7-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="119b7-515">CaptureStartupErrors</span></span>

<span data-ttu-id="119b7-516">`false`인 경우 시작 시 오류가 발생하면 호스트가 종료됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="119b7-517">`true`이면 호스트가 시작 시 예외를 캡처하고 서버 시작을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="119b7-518">**키**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="119b7-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="119b7-519">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-520">**기본값**: 기본값이 `true`인 IIS 뒤에 있는 Kestrel로 앱이 실행하지 않는 한 기본값은 `false`로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="119b7-521">**환경 변수**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="119b7-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="119b7-522">이 값을 설정하려면 구성을 사용하거나 `CaptureStartupErrors`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="119b7-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="119b7-523">DetailedErrors</span></span>

<span data-ttu-id="119b7-524">활성화하거나 환경이 `Development`인 경우 앱은 자세한 오류를 캡처합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="119b7-525">**키**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="119b7-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="119b7-526">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-527">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="119b7-527">**Default**: `false`</span></span>  
<span data-ttu-id="119b7-528">**환경 변수**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="119b7-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="119b7-529">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="119b7-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="119b7-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="119b7-531">시작 시 로드할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="119b7-532">구성 값의 기본값이 빈 문자열이지만, 호스팅 시작 어셈블리는 항상 앱의 어셈블리를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="119b7-533">호스팅 시작 어셈블리가 제공되는 경우, 시작 시 앱이 일반적인 서비스를 빌드할 때 로드를 위해 앱의 어셈블리에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="119b7-534">**키**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="119b7-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="119b7-535">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-535">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-536">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="119b7-536">**Default**: Empty string</span></span>  
<span data-ttu-id="119b7-537">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="119b7-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="119b7-538">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="119b7-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="119b7-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="119b7-540">시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="119b7-541">**키**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="119b7-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="119b7-542">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-542">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-543">**기본값**: 빈 문자열</span><span class="sxs-lookup"><span data-stu-id="119b7-543">**Default**: Empty string</span></span>  
<span data-ttu-id="119b7-544">**환경 변수**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="119b7-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="119b7-545">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="119b7-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="119b7-546">HTTPS_Port</span></span>

<span data-ttu-id="119b7-547">HTTPS 리디렉션 포트.</span><span class="sxs-lookup"><span data-stu-id="119b7-547">The HTTPS redirect port.</span></span> <span data-ttu-id="119b7-548">[HTTPS 적용](xref:security/enforcing-ssl)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="119b7-549">**키**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="119b7-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="119b7-550">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-550">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-551">**기본값**: 기본값은 설정되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="119b7-552">**환경 변수**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="119b7-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="119b7-553">이 값을 설정하려면 구성을 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="119b7-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="119b7-554">PreferHostingUrls</span></span>

<span data-ttu-id="119b7-555">호스트가 `IServer` 구현으로 구성된 URL 대신에 `IWebHostBuilder`로 구성된 URL에서 수신 대기할지 아닌지를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="119b7-556">**키**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="119b7-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="119b7-557">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-558">**기본**: `true`</span><span class="sxs-lookup"><span data-stu-id="119b7-558">**Default**: `true`</span></span>  
<span data-ttu-id="119b7-559">**환경 변수**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="119b7-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="119b7-560">이 값을 설정하려면 환경 변수를 사용하거나 `PreferHostingUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="119b7-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="119b7-561">PreventHostingStartup</span></span>

<span data-ttu-id="119b7-562">앱의 어셈블리에 의해 구성된 호스팅 시작 어셈블리를 포함한 호스팅 시작 어셈블리의 자동 로딩을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="119b7-563">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="119b7-564">**키**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="119b7-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="119b7-565">**형식**: `bool`(`true` 또는 `1`)</span><span class="sxs-lookup"><span data-stu-id="119b7-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="119b7-566">**기본**: `false`</span><span class="sxs-lookup"><span data-stu-id="119b7-566">**Default**: `false`</span></span>  
<span data-ttu-id="119b7-567">**환경 변수**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="119b7-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="119b7-568">이 값을 설정하려면 환경 변수를 사용하거나 `UseSetting`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="119b7-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="119b7-569">StartupAssembly</span></span>

<span data-ttu-id="119b7-570">`Startup` 클래스를 검색할 어셈블리입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="119b7-571">**키**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="119b7-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="119b7-572">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-572">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-573">**기본값**: 앱의 어셈블리</span><span class="sxs-lookup"><span data-stu-id="119b7-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="119b7-574">**환경 변수**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="119b7-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="119b7-575">이 값을 설정하려면 환경 변수를 사용하거나 `UseStartup`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="119b7-576">`UseStartup`은 어셈블리 이름(`string`) 또는 형식(`TStartup`)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="119b7-577">`UseStartup` 메서드가 여러 개 호출된 경우 마지막 메서드가 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="119b7-578">URL</span><span class="sxs-lookup"><span data-stu-id="119b7-578">URLs</span></span>

<span data-ttu-id="119b7-579">서버에서 요청을 수신해야 하는 포트와 프로토콜을 포함하는 세미클론으로 구분된 IP 주소 또는 호스트 주소의 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="119b7-580">예: `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="119b7-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="119b7-581">“\*”를 사용하여 서버가 지정된 포트 및 프로토콜을 사용하는 IP 주소 또는 호스트 이름에서 요청을 수신해야 함을 나타냅니다(예: `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="119b7-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="119b7-582">프로토콜(`http://` 또는 `https://`)은 각 URL에 포함되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="119b7-583">지원되는 형식은 서버마다 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="119b7-584">**키**: `urls`</span><span class="sxs-lookup"><span data-stu-id="119b7-584">**Key**: `urls`</span></span>  
<span data-ttu-id="119b7-585">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-585">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-586">**기본값**: `http://localhost:5000` 및 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="119b7-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="119b7-587">**환경 변수**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="119b7-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="119b7-588">이 값을 설정하려면 환경 변수를 사용하거나 `UseUrls`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="119b7-589">Kestrel에는 자체 엔드포인트 구성 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="119b7-590">자세한 내용은 <xref:fundamentals/servers/kestrel#endpoint-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="119b7-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-591">WebRoot</span></span>

<span data-ttu-id="119b7-592">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 속성은 앱 정적 자산의 상대 경로를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="119b7-593">경로가 존재하지 않으면 no-op 파일 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="119b7-594">**키**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="119b7-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="119b7-595">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-595">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-596">**기본값**: 기본값은 `wwwroot`입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="119b7-597">*{content root}/wwwroot* 경로가 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="119b7-598">**환경 변수**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="119b7-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="119b7-599">이 값을 설정하려면 환경 변수를 사용하거나 `IWebHostBuilder`에서 `UseWebRoot`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="119b7-600">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-600">For more information, see:</span></span>

* [<span data-ttu-id="119b7-601">기본 사항: 웹 루트</span><span class="sxs-lookup"><span data-stu-id="119b7-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="119b7-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="119b7-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="119b7-603">호스트 수명 관리</span><span class="sxs-lookup"><span data-stu-id="119b7-603">Manage the host lifetime</span></span>

<span data-ttu-id="119b7-604">기본 제공된 <xref:Microsoft.Extensions.Hosting.IHost> 구현에 대한 메서드를 호출하여 애플리케이션을 시작하고 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="119b7-605">이러한 메서드는 서비스 컨테이너에 등록된 모든 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="119b7-606">Run</span><span class="sxs-lookup"><span data-stu-id="119b7-606">Run</span></span>

<span data-ttu-id="119b7-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="119b7-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-608">RunAsync</span></span>

<span data-ttu-id="119b7-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="119b7-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-610">RunConsoleAsync</span></span>

<span data-ttu-id="119b7-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="119b7-612">Start</span><span class="sxs-lookup"><span data-stu-id="119b7-612">Start</span></span>

<span data-ttu-id="119b7-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="119b7-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-614">StartAsync</span></span>

<span data-ttu-id="119b7-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>는 호스트를 시작하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="119b7-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 `StartAsync`의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="119b7-617">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="119b7-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-618">StopAsync</span></span>

<span data-ttu-id="119b7-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="119b7-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="119b7-620">WaitForShutdown</span></span>

<span data-ttu-id="119b7-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 통해 IHostLifetime에 의해 종료가 트리거될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="119b7-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="119b7-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="119b7-624">외부 제어</span><span class="sxs-lookup"><span data-stu-id="119b7-624">External control</span></span>

<span data-ttu-id="119b7-625">호스트 수명에 대한 직접 제어는 외부에서 호출할 수 있는 메서드를 사용하여 달성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="119b7-626">ASP.NET Core 앱은 호스트를 구성 및 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="119b7-627">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="119b7-628">이 문서에서는 HTTP 요청을 처리하지 않는 앱에 사용되는 ASP.NET Core 일반 호스트(<xref:Microsoft.Extensions.Hosting.HostBuilder>)를 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="119b7-629">일반 호스트의 목적은 웹 호스트 API에서 HTTP 파이프라인을 분리하여 호스트 시나리오의 더 광범위한 배열을 구현하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="119b7-630">일반 호스트에 기반을 둔 메시징, 백그라운드 작업 및 기타 HTTP 이외 워크로드는 구성, DI(종속성 주입) 및 로깅과 같은 교차 편집 기능에서 이점을 얻습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="119b7-631">일반 호스트는 ASP.NET Core 2.1의 새로운 기능이며 웹 호스팅 시나리오에 적합하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="119b7-632">웹 호스팅 시나리오의 경우 [웹 호스트](xref:fundamentals/host/web-host)를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="119b7-633">일반 호스트는 향후 릴리스에서 웹 호스트를 대체하고 HTTP 및 HTTP 이외 시나리오에서 기본 호스트 API 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="119b7-634">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="119b7-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="119b7-635">[Visual Studio Code](https://code.visualstudio.com/)에서 샘플 앱을 실행할 때는 *외부 또는 통합 터미널* 을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="119b7-636">`internalConsole`에서는 샘플을 실행하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="119b7-637">Visual Studio Code에서 콘솔을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="119b7-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="119b7-638">*.vscode/launch.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="119b7-639">**.NET Core 시작(콘솔)** 구성에서 **콘솔** 항목을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="119b7-640">값을 `externalTerminal` 또는 `integratedTerminal` 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="119b7-641">소개</span><span class="sxs-lookup"><span data-stu-id="119b7-641">Introduction</span></span>

<span data-ttu-id="119b7-642">일반 호스트 라이브러리는 <xref:Microsoft.Extensions.Hosting> 네임스페이스에서 사용할 수 있으며, [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="119b7-643">`Microsoft.Extensions.Hosting` 패키지는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)(ASP.NET Core 2.1 이상)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="119b7-644"><xref:Microsoft.Extensions.Hosting.IHostedService>는 코드 실행 진입점입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="119b7-645">각 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현은 [ConfigureServices의 서비스 등록](#configureservices) 순서대로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="119b7-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>는 호스트가 시작될 때 각 <xref:Microsoft.Extensions.Hosting.IHostedService>에서 호출되고, <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>는 호스트가 점진적으로 종료될 때 등록 순서의 역순으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="119b7-647">호스트 설정</span><span class="sxs-lookup"><span data-stu-id="119b7-647">Set up a host</span></span>

<span data-ttu-id="119b7-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder>는 라이브러리 및 앱이 호스트를 초기화, 빌드 및 실행하는 데 사용하는 주 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="119b7-649">옵션</span><span class="sxs-lookup"><span data-stu-id="119b7-649">Options</span></span>

<span data-ttu-id="119b7-650"><xref:Microsoft.Extensions.Hosting.HostOptions>는 <xref:Microsoft.Extensions.Hosting.IHost>에 대한 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="119b7-651">시스템 종료 시간 제한</span><span class="sxs-lookup"><span data-stu-id="119b7-651">Shutdown timeout</span></span>

<span data-ttu-id="119b7-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>에 대한 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="119b7-653">기본값은 5초입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-653">The default value is five seconds.</span></span>

<span data-ttu-id="119b7-654">`Program.Main`의 다음 옵션 구성은 기본 5초 시스템 종료 시간 제한을 20초로 늘립니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="119b7-655">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="119b7-655">Default services</span></span>

<span data-ttu-id="119b7-656">호스트 초기화 중에 다음 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="119b7-657">[환경](xref:fundamentals/environments)(<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="119b7-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="119b7-658">[구성](xref:fundamentals/configuration/index)(<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="119b7-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="119b7-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="119b7-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="119b7-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="119b7-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="119b7-661">[옵션](xref:fundamentals/configuration/options)(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="119b7-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="119b7-662">[로깅](xref:fundamentals/logging/index)(<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="119b7-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="119b7-663">호스트 구성</span><span class="sxs-lookup"><span data-stu-id="119b7-663">Host configuration</span></span>

<span data-ttu-id="119b7-664">호스트 구성은 다음에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-664">Host configuration is created by:</span></span>

* <span data-ttu-id="119b7-665"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 호출하여 [콘텐츠 루트](#content-root) 및 [환경](#environment)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="119b7-666"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>의 구성 공급자에서 구성을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="119b7-667">확장 메서드</span><span class="sxs-lookup"><span data-stu-id="119b7-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="119b7-668">애플리케이션 키(이름)</span><span class="sxs-lookup"><span data-stu-id="119b7-668">Application key (name)</span></span>

<span data-ttu-id="119b7-669">호스트를 생성하는 동안 호스트 구성에서 [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 속성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="119b7-670">값을 명시적으로 설정하려면 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="119b7-671">**키**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="119b7-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="119b7-672">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-672">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-673">**기본값**: 앱의 진입점을 포함하는 어셈블리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="119b7-674">**설정 방법**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="119b7-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="119b7-675">**환경 변수**: `<PREFIX_>APPLICATIONNAME`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="119b7-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="119b7-676">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="119b7-676">Content root</span></span>

<span data-ttu-id="119b7-677">이 설정은 호스트가 콘텐츠 파일을 검색하기 시작하는 지점을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="119b7-678">**키**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="119b7-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="119b7-679">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-679">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-680">**기본값**: 앱 어셈블리가 있는 폴더가 기본값으로 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="119b7-681">**설정 방법**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="119b7-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="119b7-682">**환경 변수**: `<PREFIX_>CONTENTROOT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="119b7-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="119b7-683">경로가 존재하지 않는 경우 호스트가 시작되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="119b7-684">자세한 내용은 [기본 사항: 콘텐츠 루트](xref:fundamentals/index#content-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="119b7-685">환경</span><span class="sxs-lookup"><span data-stu-id="119b7-685">Environment</span></span>

<span data-ttu-id="119b7-686">앱의 [환경](xref:fundamentals/environments)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="119b7-687">**키**: `environment`</span><span class="sxs-lookup"><span data-stu-id="119b7-687">**Key**: `environment`</span></span>  
<span data-ttu-id="119b7-688">**형식**: `string`</span><span class="sxs-lookup"><span data-stu-id="119b7-688">**Type**: `string`</span></span>  
<span data-ttu-id="119b7-689">**기본**: `Production`</span><span class="sxs-lookup"><span data-stu-id="119b7-689">**Default**: `Production`</span></span>  
<span data-ttu-id="119b7-690">**설정 방법**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="119b7-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="119b7-691">**환경 변수**: `<PREFIX_>ENVIRONMENT`(`<PREFIX_>`는 [선택적이고 사용자 정의됨](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="119b7-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="119b7-692">환경은 어떠한 값으로도 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-692">The environment can be set to any value.</span></span> <span data-ttu-id="119b7-693">프레임워크에서 정의된 값은 `Development`, `Staging` 및 `Production`을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="119b7-694">값은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="119b7-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="119b7-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="119b7-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 호스트에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="119b7-697">호스트 구성은 앱의 빌드 프로세스에 사용할 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 초기화하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="119b7-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="119b7-699">호스트는 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="119b7-700">기본적으로 공급자는 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-700">No providers are included by default.</span></span> <span data-ttu-id="119b7-701">다음을 포함하여 앱에 필요한 모든 구성 공급자를 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>에 명시적으로 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="119b7-702">구성 파일(예: *hostsettings.json* 파일에서).</span><span class="sxs-lookup"><span data-stu-id="119b7-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="119b7-703">환경 변수 구성.</span><span class="sxs-lookup"><span data-stu-id="119b7-703">Environment variable configuration.</span></span>
* <span data-ttu-id="119b7-704">명령줄 인수 구성.</span><span class="sxs-lookup"><span data-stu-id="119b7-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="119b7-705">기타 필수 구성 공급자.</span><span class="sxs-lookup"><span data-stu-id="119b7-705">Any other required configuration providers.</span></span>

<span data-ttu-id="119b7-706">호스트의 파일 구성은 `SetBasePath`를 사용하여 앱의 기본 경로를 지정한 후 [파일 구성 공급자](xref:fundamentals/configuration/index#file-configuration-provider) 중 하나를 호출하여 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="119b7-707">샘플 앱은 JSON 파일인 *hostsettings.json* 을 사용하고 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>을 호출하여 파일의 호스트 구성 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="119b7-708">[환경 변수 구성](xref:fundamentals/configuration/index#environment-variables-configuration-provider)을 추가하려면 호스트 작성기에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="119b7-709">`AddEnvironmentVariables`는 선택적 사용자 정의 접두사를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="119b7-710">샘플 앱은 `PREFIX_` 접두사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="119b7-711">접두사는 환경 변수를 읽을 때 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="119b7-712">샘플 앱의 호스트가 구성되면 `PREFIX_ENVIRONMENT`의 환경 변수 값이 `environment` 키에 대한 호스트 구성 값이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="119b7-713">[Visual Studio](https://visualstudio.microsoft.com)를 사용하거나 `dotnet run`을 통해 앱을 실행할 때 개발하는 동안에 환경 변수는 *Properties/launchSettings.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="119b7-714">[Visual Studio Code](https://code.visualstudio.com/)에서 환경 변수는 개발하는 동안에 *.vscode/launch.json* 파일에 설정될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="119b7-715">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="119b7-716">[명령줄 구성](xref:fundamentals/configuration/index#command-line-configuration-provider)은 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>을 호출하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="119b7-717">명령줄 구성이 마지막으로 추가되어 명령줄 인수가 이전 구성 공급자가 제공한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="119b7-718">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="119b7-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="119b7-719">[applicationName](#application-key-name) 및 [contentRoot](#content-root) 키를 사용하여 추가 구성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="119b7-720"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 사용한 `HostBuilder` 구성 예:</span><span class="sxs-lookup"><span data-stu-id="119b7-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="119b7-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="119b7-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="119b7-722">앱 구성은 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현에서 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 호출하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="119b7-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>은 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하여 앱에 대한 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="119b7-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="119b7-725">앱은 지정된 키에 마지막으로 값을 설정하는 옵션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="119b7-726"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>에 의해 생성된 구성은 다음 작업에 대한 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 및 <xref:Microsoft.Extensions.Hosting.IHost.Services*>에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="119b7-727">앱 구성은 [ConfigureHostConfiguration](#configurehostconfiguration)에서 제공하는 호스트 구성을 자동으로 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="119b7-728"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>을 사용한 앱 구성 예:</span><span class="sxs-lookup"><span data-stu-id="119b7-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="119b7-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="119b7-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="119b7-730">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="119b7-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="119b7-731">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="119b7-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="119b7-732">출력 디렉터리로 설정 파일을 이동하려면, 설정 파일을 프로젝트 파일 내 [MSBuild 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items)으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="119b7-733">샘플 앱은 다음 `<Content>` 항목과 함께 JSON 앱 설정 파일과 *hostsettings.json* 을 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="119b7-734"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 및 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>와 같은 구성 확장 메서드에는 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 및 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)와 같은 추가 NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="119b7-735">앱에서 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하는 경우가 아니면 이 패키지는 코어 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 패키지에 추가로 프로젝트에 추가되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="119b7-736">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="119b7-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="119b7-737">ConfigureServices</span></span>

<span data-ttu-id="119b7-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>는 앱의 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="119b7-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="119b7-740">호스티드 서비스는 <xref:Microsoft.Extensions.Hosting.IHostedService> 인터페이스를 구현하는 백그라운드 작업 논리가 있는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="119b7-741">자세한 내용은 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="119b7-742">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `AddHostedService` 확장 메서드를 사용하여 수명 이벤트, `LifetimeEventsHostedService` 및 시간 제한 백그라운드 작업에 대한 서비스 `TimedHostedService`를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="119b7-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="119b7-743">ConfigureLogging</span></span>

<span data-ttu-id="119b7-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>에는 제공된 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>를 구성하는 대리자가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="119b7-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="119b7-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="119b7-746">UseConsoleLifetime</span></span>

<span data-ttu-id="119b7-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM을 수신 대기하고 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 호출하여 종료 프로세스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="119b7-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>은 [RunAsync](#runasync) 및 [WaitForShutdownAsync](#waitforshutdownasync)와 같은 확장의 차단을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="119b7-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`은 기본 수명 구현으로 미리 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="119b7-750">등록된 마지막 수명이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="119b7-751">컨테이너 구성</span><span class="sxs-lookup"><span data-stu-id="119b7-751">Container configuration</span></span>

<span data-ttu-id="119b7-752">호스트는 다른 컨테이너 연결을 지원하기 위해 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="119b7-753">팩터리 제공은 DI 컨테이너 등록의 일부가 아니지만 구체적 DI 컨테이너를 만드는 데 사용되는 내장 호스트입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="119b7-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*)는 앱의 서비스 공급자를 만드는 데 사용되는 기본 팩터리를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="119b7-755">사용자 지정 컨테이너 구성은 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 메서드로 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="119b7-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>는 기본 호스트 API 위에 컨테이너를 구성하기 위한 강력한 형식의 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="119b7-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="119b7-758">앱에 대한 서비스 컨테이너를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="119b7-759">서비스 컨테이너 팩터리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="119b7-760">팩터리를 사용하고 앱에 대 한 사용자 지정 서비스 컨테이너를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="119b7-761">확장성</span><span class="sxs-lookup"><span data-stu-id="119b7-761">Extensibility</span></span>

<span data-ttu-id="119b7-762"><xref:Microsoft.Extensions.Hosting.IHostBuilder>에서 확장 메서드를 사용하여 호스트 확장성이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="119b7-763">다음 예제는 <xref:fundamentals/host/hosted-services>에 설명된 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 예제를 사용하여 확장 메서드가 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 구현을 확장하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="119b7-764">앱은 `UseHostedService` 확장 메서드를 설정하여 `T`에서 전달되는 호스티드 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="119b7-765">호스트 관리</span><span class="sxs-lookup"><span data-stu-id="119b7-765">Manage the host</span></span>

<span data-ttu-id="119b7-766"><xref:Microsoft.Extensions.Hosting.IHost> 구현은 서비스 컨테이너에 등록된 <xref:Microsoft.Extensions.Hosting.IHostedService> 구현의 시작 및 중지를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="119b7-767">Run</span><span class="sxs-lookup"><span data-stu-id="119b7-767">Run</span></span>

<span data-ttu-id="119b7-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>은 앱을 시작하고 호스트가 종료될 때까지 호출 스레드를 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="119b7-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-769">RunAsync</span></span>

<span data-ttu-id="119b7-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>는 앱을 실행하고 취소 토큰 또는 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="119b7-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-771">RunConsoleAsync</span></span>

<span data-ttu-id="119b7-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>는 콘솔을 지원하고 호스트를 빌드 및 시작하며 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM이 종료될 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="119b7-773">Start 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-773">Start and StopAsync</span></span>

<span data-ttu-id="119b7-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>는 호스트를 동기적으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="119b7-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>는 지정된 시간 제한 내에서 호스트를 중지하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="119b7-776">StartAsync 및 StopAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="119b7-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>가 서버를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="119b7-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>가 서버를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="119b7-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="119b7-779">WaitForShutdown</span></span>

<span data-ttu-id="119b7-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`(<kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 또는 SIGTERM 수신 대기)과 같이 <xref:Microsoft.Extensions.Hosting.IHostLifetime>을 통해 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="119b7-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>은 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="119b7-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="119b7-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="119b7-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>는 지정된 토큰을 통해 종료가 트리거될 때 완료되는 <xref:System.Threading.Tasks.Task>를 반환하고 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="119b7-784">외부 제어</span><span class="sxs-lookup"><span data-stu-id="119b7-784">External control</span></span>

<span data-ttu-id="119b7-785">외부에서 호출할 수 있는 메서드를 사용하여 호스트의 외부 제어를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-785">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="119b7-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>는 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>의 시작 시 호출되고, 완료될 때까지 기다린 후 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="119b7-787">이는 외부 이벤트에서 신호를 보낼 때까지 시작을 지연시키는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="119b7-788">IHostingEnvironment 인터페이스</span><span class="sxs-lookup"><span data-stu-id="119b7-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="119b7-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 호스팅 환경에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="119b7-790">해당 속성 및 확장 메서드를 사용하기 위해 [생성자 주입](xref:fundamentals/dependency-injection)을 사용하여 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="119b7-791">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="119b7-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="119b7-792">IApplicationLifetime 인터페이스</span><span class="sxs-lookup"><span data-stu-id="119b7-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="119b7-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime>은 점진적인 종료 요청을 비롯한 사후 시작 및 종료 작업을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="119b7-794">인터페이스에서 세 가지 속성은 취소 토큰으로, 시작 및 종료 이벤트를 정의하는 <xref:System.Action> 메서드를 등록하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="119b7-795">취소 토큰</span><span class="sxs-lookup"><span data-stu-id="119b7-795">Cancellation Token</span></span> | <span data-ttu-id="119b7-796">트리거되는 경우:</span><span class="sxs-lookup"><span data-stu-id="119b7-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="119b7-797">호스트가 완벽하게 시작되었습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="119b7-798">호스트가 정상적으로 종료되었습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="119b7-799">모든 요청이 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-799">All requests should be processed.</span></span> <span data-ttu-id="119b7-800">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="119b7-801">호스트가 정상적으로 종료되고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="119b7-802">요청은 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-802">Requests may still be processing.</span></span> <span data-ttu-id="119b7-803">종료는 이 이벤트가 완료될 때까지 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="119b7-804">클래스에 대한 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 서비스 생성자 주입을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="119b7-805">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)은 `LifetimeEventsHostedService` 클래스(<xref:Microsoft.Extensions.Hosting.IHostedService> 구현)에 대한 생성자 주입을 사용하여 이벤트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="119b7-806">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="119b7-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="119b7-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>은 앱의 종료를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="119b7-808">다음 클래스에서는 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>을 사용하여 해당 클래스의 `Shutdown` 메서드를 호출하는 경우 앱을 정상 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="119b7-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="119b7-809">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="119b7-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
