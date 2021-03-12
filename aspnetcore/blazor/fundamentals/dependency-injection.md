---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor 앱이 구성 요소에 서비스를 주입할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: aefeac2f3a68a669b7c84cbeee2f6a4ec0621f6f
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109678"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="f13e4-103">ASP.NET Core Blazor 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="f13e4-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="f13e4-104">작성자: [Rainer Stropek](https://www.timecockpit.com) 및 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="f13e4-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="f13e4-105">[DI(종속성 주입)](xref:fundamentals/dependency-injection)는 중앙 위치에 구성된 서비스에 액세스하기 위한 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="f13e4-106">프레임워크 등록 서비스는 Blazor 앱의 구성 요소에 직접 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="f13e4-107">Blazor 앱은 사용자 지정 서비스를 정의 및 등록하고 DI를 통해 앱 전체에서 사용하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="f13e4-108">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="f13e4-108">Default services</span></span>

<span data-ttu-id="f13e4-109">다음 표에 표시된 서비스는 일반적으로 Blazor 앱에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-109">The services shown in the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="f13e4-110">서비스</span><span class="sxs-lookup"><span data-stu-id="f13e4-110">Service</span></span> | <span data-ttu-id="f13e4-111">수명</span><span class="sxs-lookup"><span data-stu-id="f13e4-111">Lifetime</span></span> | <span data-ttu-id="f13e4-112">설명</span><span class="sxs-lookup"><span data-stu-id="f13e4-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="f13e4-113">Scoped</span><span class="sxs-lookup"><span data-stu-id="f13e4-113">Scoped</span></span> | <p><span data-ttu-id="f13e4-114">URI로 식별되는 리소스에서 HTTP 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="f13e4-115">Blazor WebAssembly 앱의 <xref:System.Net.Http.HttpClient> 인스턴스는 브라우저를 사용하여 백그라운드에서 HTTP 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="f13e4-116">Blazor Server 앱에는 기본적으로 서비스로 구성된 <xref:System.Net.Http.HttpClient>는 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="f13e4-117">Blazor Server 앱에 <xref:System.Net.Http.HttpClient>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="f13e4-118">자세한 내용은 <xref:blazor/call-web-api>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="f13e4-119"><xref:System.Net.Http.HttpClient>가 싱글톤이 아닌 범위가 지정된 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="f13e4-120">자세한 내용은 [서비스 수명](#service-lifetime) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="f13e4-121">**Blazor WebAssembly** : Singleton</span><span class="sxs-lookup"><span data-stu-id="f13e4-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="f13e4-122">**Blazor Server** : Scoped</span><span class="sxs-lookup"><span data-stu-id="f13e4-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="f13e4-123">JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="f13e4-124">자세한 내용은 <xref:blazor/call-javascript-from-dotnet>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="f13e4-125">**Blazor WebAssembly** : Singleton</span><span class="sxs-lookup"><span data-stu-id="f13e4-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="f13e4-126">**Blazor Server** : Scoped</span><span class="sxs-lookup"><span data-stu-id="f13e4-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="f13e4-127">URI 및 탐색 상태를 사용하기 위한 도우미를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="f13e4-128">자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="f13e4-129">사용자 지정 서비스 공급자는 테이블에 나열된 기본 서비스를 자동으로 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="f13e4-130">사용자 지정 서비스 공급자를 사용하고 표에 표시된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="f13e4-131">앱에 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="f13e4-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="f13e4-132">`Program.cs`의 `Program.Main` 메서드에서 앱의 서비스 컬렉션용 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="f13e4-133">다음 예제에서는 `MyDependency` 구현을 `IMyDependency`에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="f13e4-134">호스트를 빌드한 후 구성 요소를 렌더링하기 전에 루트 DI 범위에서 서비스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="f13e4-135">이러한 기능은 콘텐츠를 렌더링하기 전에 초기화 논리를 실행하는 데 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-135">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="f13e4-136">호스트는 앱의 중앙 구성 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="f13e4-137">이전 예제를 기준으로 작성한 날씨 서비스 URL은 기본 구성 원본(예: `appsettings.json`)에서 `InitializeWeatherAsync`로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="f13e4-138">새 앱을 만든 후 `Startup.ConfigureServices`에서 `Startup.cs` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="f13e4-139"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> 메서드는 [서비스 설명자](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) 개체 목록에 해당하는 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="f13e4-140">서비스 설명자를 서비스 컬렉션에 제공함으로써 서비스가 `ConfigureServices` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="f13e4-141">다음 예제에서는 `IDataAccess` 인터페이스와 해당 구체적 구현 `DataAccess`를 통해 이러한 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="f13e4-142">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="f13e4-142">Service lifetime</span></span>

<span data-ttu-id="f13e4-143">다음 표에 표시된 수명으로 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="f13e4-144">수명</span><span class="sxs-lookup"><span data-stu-id="f13e4-144">Lifetime</span></span> | <span data-ttu-id="f13e4-145">설명</span><span class="sxs-lookup"><span data-stu-id="f13e4-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="f13e4-146">Blazor WebAssembly 앱에는 현재, DI 범위에 대한 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="f13e4-147">`Scoped` 등록 서비스는 `Singleton` 서비스처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="f13e4-148">Blazor Server 호스팅 모델은 HTTP 요청에서 `Scoped` 수명을 지원하지만 클라이언트에서 로드되는 구성 요소 간의 SignalR 연결/회로 메시지에서는 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SignalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="f13e4-149">Razor Pages 또는 앱의 MVC 부분은 범위가 지정된 서비스를 정상적으로 처리하고, 페이지 또는 뷰 사이를 이동하거나 페이지 또는 뷰에서 구성 요소로 이동할 때 각 HTTP 요청에서 서비스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="f13e4-150">클라이언트의 구성 요소 사이를 이동할 때는 범위가 지정된 서비스가 다시 구성되지 않습니다. 이 경우 서버와의 통신은 HTTP 요청을 통하지 않고 사용자 회로의 SignalR 연결을 통해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="f13e4-151">클라이언트에서 다음과 같은 구성 요소 시나리오에서는 사용자를 위해 새 회로가 만들어지므로 범위가 지정된 서비스가 다시 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="f13e4-152">사용자가 브라우저의 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-152">The user closes the browser's window.</span></span> <span data-ttu-id="f13e4-153">사용자가 새 창을 열고 앱으로 다시 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="f13e4-154">사용자가 브라우저 창에서 앱의 마지막 탭을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="f13e4-155">사용자가 새 탭을 열고 앱으로 다시 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="f13e4-156">사용자가 브라우저의 다시 로드/새로 고침 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="f13e4-157">Blazor Server 앱의 범위가 지정된 서비스에서 사용자 상태를 보존하는 방법에 대한 자세한 내용은 <xref:blazor/hosting-models?pivots=server>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="f13e4-158">DI는 서비스의 *단일 인스턴스* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="f13e4-159">`Singleton` 서비스가 필요한 모든 구성 요소는 동일한 서비스의 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="f13e4-160">구성 요소는 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="f13e4-161">DI 시스템은 ASP.NET Core에서 DI 시스템을 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="f13e4-162">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="f13e4-163">구성 요소에서 서비스 요청</span><span class="sxs-lookup"><span data-stu-id="f13e4-163">Request a service in a component</span></span>

<span data-ttu-id="f13e4-164">서비스 컬렉션에 서비스를 추가한 후에는 다음 두 개의 매개 변수를 포함하는 [`@inject`](xref:mvc/views/razor#inject) Razor 지시문을 사용하여 서비스를 구성 요소에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-164">After services are added to the service collection, inject the services into the components using the [`@inject`](xref:mvc/views/razor#inject) Razor directive, which has two parameters:</span></span>

* <span data-ttu-id="f13e4-165">유형: 주입할 서비스의 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="f13e4-166">속성: 주입된 앱 서비스를 받는 속성의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="f13e4-167">이 속성은 수동으로 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="f13e4-168">컴파일러에서 속성을 만들기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-168">The compiler creates the property.</span></span>

<span data-ttu-id="f13e4-169">자세한 내용은 <xref:mvc/views/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="f13e4-170">여러 [`@inject`](xref:mvc/views/razor#inject) 문을 사용하여 여러 서비스를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="f13e4-171">다음 예제에서는 [`@inject`](xref:mvc/views/razor#inject)를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="f13e4-172">`Services.IDataAccess`를 구현하는 서비스는 구성 요소의 속성 `DataRepository`에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="f13e4-173">코드가 `IDataAccess` 추상화만 사용하는 방식에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

<span data-ttu-id="f13e4-174">내부적으로 생성된 속성(`DataRepository`)은 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span></span> <span data-ttu-id="f13e4-175">일반적으로 이 특성은 직접 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="f13e4-176">구성 요소에 기본 클래스가 필요하고 기본 클래스에 주입된 속성도 필요하면 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute)을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="f13e4-177">기본 클래스에서 파생된 구성 요소에서는 [`@inject`](xref:mvc/views/razor#inject) 지시문이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="f13e4-178">기본 클래스의 <xref:Microsoft.AspNetCore.Components.InjectAttribute>만 있으면 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="f13e4-179">서비스에서 DI 사용</span><span class="sxs-lookup"><span data-stu-id="f13e4-179">Use DI in services</span></span>

<span data-ttu-id="f13e4-180">복잡한 서비스에는 추가 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-180">Complex services might require additional services.</span></span> <span data-ttu-id="f13e4-181">다음 예제에서는 `DataAccess`에 <xref:System.Net.Http.HttpClient> 기본 서비스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-181">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="f13e4-182">[`@inject`](xref:mvc/views/razor#inject)(또는 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute))는 서비스에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute)) isn't available for use in services.</span></span> <span data-ttu-id="f13e4-183">대신 *생성자 주입* 을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="f13e4-184">서비스의 생성자에 매개 변수를 추가하여 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="f13e4-185">DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식하고 적절히 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="f13e4-186">다음 예제에서 생성자는 DI를 통해 <xref:System.Net.Http.HttpClient>를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="f13e4-187"><xref:System.Net.Http.HttpClient>는 기본 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="f13e4-188">생성자 주입의 필수 조건:</span><span class="sxs-lookup"><span data-stu-id="f13e4-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="f13e4-189">모든 인수를 DI에서 처리할 수 있는 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="f13e4-190">DI에서 다루지 않는 추가 매개 변수는 기본값이 지정되면 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="f13e4-191">적용 가능한 생성자는 `public`이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="f13e4-192">적용 가능한 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-192">One applicable constructor must exist.</span></span> <span data-ttu-id="f13e4-193">모호한 경우 시 DI는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="f13e4-194">DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="f13e4-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="f13e4-195">ASP.NET Core 앱에서 범위가 지정된 서비스는 일반적으로 현재 요청으로 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="f13e4-196">요청이 완료된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="f13e4-197">Blazor Server 앱에서 요청 범위는 클라이언트 연결 기간에 지속되므로 임시 및 범위가 지정된 서비스가 예상보다 훨씬 오래 지속될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="f13e4-198">Blazor WebAssembly 앱에서, 범위가 지정된 수명으로 등록된 서비스는 singleton으로 처리되므로 일반적인 ASP.NET Core 앱의 범위가 지정된 서비스보다 오래 지속됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="f13e4-199">앱에서 삭제 가능한 임시 서비스를 검색하려면 [임시 삭제 가능 항목 검색](#detect-transient-disposables) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="f13e4-200">Blazor 앱에서 서비스 수명을 제한하는 방법은 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="f13e4-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 구성 요소의 수명에 해당하는 DI 범위를 만드는 <xref:Microsoft.AspNetCore.Components.ComponentBase>에서 파생된 추상 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="f13e4-202">이 범위를 사용하는 경우 수명 범위를 지정한 DI 서비스를 사용하고 해당 구성 요소만큼 지속되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="f13e4-203">구성 요소가 제거되면 구성 요소 범위 지정 서비스 공급자의 서비스도 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="f13e4-204">이 기능은 다음과 같은 서비스에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-204">This can be useful for services that:</span></span>

* <span data-ttu-id="f13e4-205">일시적 수명은 부적절하므로 구성 요소 내에서 다시 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="f13e4-206">싱글톤 수명은 부적절하므로 구성 요소에서 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="f13e4-207">두 가지 버전의 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="f13e4-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 <xref:System.IServiceProvider> 형식의 보호된 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase> 형식의 삭제 가능한 추상 자식입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f13e4-209">이 공급자는 구성 요소의 수명으로 범위가 지정된 서비스를 확인하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="f13e4-210">[`@inject`](xref:mvc/views/razor#inject) 또는 [`[Inject]` 특성](xref:Microsoft.AspNetCore.Components.InjectAttribute)을 사용하여 구성 요소에 주입된 DI 서비스는 구성 요소의 범위에 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute) aren't created in the component's scope.</span></span> <span data-ttu-id="f13e4-211">구성 요소의 범위를 사용하려면 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 또는 <xref:System.IServiceProvider.GetService%2A>를 사용하여 서비스를 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="f13e4-212"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 공급자를 사용하여 확인된 모든 서비스에는 동일한 범위에서 종속성이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

* <span data-ttu-id="f13e4-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>에서 파생되고 범위가 지정된 DI 공급자에서 `T`의 인스턴스를 반환하는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="f13e4-214">이 형식은 구성 요소의 범위를 사용하여 DI 컨테이너에서 앱에 필요한 기본 서비스가 하나 있는 경우 <xref:System.IServiceProvider> 인스턴스를 사용하지 않고 범위 지정 서비스에 액세스할 수 있는 편리한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="f13e4-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용할 수 있으므로 필요한 경우 앱에서 다른 형식의 서비스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="f13e4-216">DI에서 EF Core(Entity Framework Core) DbContext 사용</span><span class="sxs-lookup"><span data-stu-id="f13e4-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="f13e4-217">자세한 내용은 <xref:blazor/blazor-server-ef-core>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="f13e4-218">임시 삭제 가능 항목 검색</span><span class="sxs-lookup"><span data-stu-id="f13e4-218">Detect transient disposables</span></span>

<span data-ttu-id="f13e4-219">다음 예제에서는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>를 사용해야 하는 앱에서 삭제 가능한 임시 서비스를 검색하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="f13e4-220">자세한 내용은 [DI 범위를 관리하는 유틸리티 기본 구성 요소 클래스](#utility-base-component-classes-to-manage-a-di-scope) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f13e4-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="f13e4-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="f13e4-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

<span data-ttu-id="f13e4-222">다음 예제에서 `TransientDisposable`이 검색되었습니다(`Program.cs`).</span><span class="sxs-lookup"><span data-stu-id="f13e4-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("#app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="f13e4-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="f13e4-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

<span data-ttu-id="f13e4-224"><xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>의 네임스페이스를 `Program.cs`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-224">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="f13e4-225">`Program.cs`의 `Program.CreateHostBuilder`에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-225">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .DetectIncorrectUsageOfTransients()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="f13e4-226">다음 예제에서 `TransientDependency`가 검색되었습니다(`Startup.cs`).</span><span class="sxs-lookup"><span data-stu-id="f13e4-226">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
    services.AddServerSideBlazor();
    services.AddSingleton<WeatherForecastService>();
    services.AddTransient<TransientDependency>();
    services.AddTransient<ITransitiveTransientDisposableDependency, 
        TransitiveTransientDisposableDependency>();
}

public class TransitiveTransientDisposableDependency 
    : ITransitiveTransientDisposableDependency, IDisposable
{
    public void Dispose() { }
}

public interface ITransitiveTransientDisposableDependency
{
}

public class TransientDependency
{
    private readonly ITransitiveTransientDisposableDependency 
        _transitiveTransientDisposableDependency;

    public TransientDependency(ITransitiveTransientDisposableDependency 
        transitiveTransientDisposableDependency)
    {
        _transitiveTransientDisposableDependency = 
            transitiveTransientDisposableDependency;
    }
}
```

::: zone-end

<span data-ttu-id="f13e4-227">앱은 예외를 throw하지 않고 삭제 가능한 임시 서비스를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-227">The app can register transient disposables without throwing an exception.</span></span> <span data-ttu-id="f13e4-228">그러나 다음 예제에 나와 있는 것처럼 삭제 가능한 임시 서비스를 확인하려는 경우 <xref:System.InvalidOperationException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-228">However, attempting to resolve a transient disposable results in an <xref:System.InvalidOperationException>, as the following example shows.</span></span>

<span data-ttu-id="f13e4-229">`Pages/TransientDisposable.razor`:</span><span class="sxs-lookup"><span data-stu-id="f13e4-229">`Pages/TransientDisposable.razor`:</span></span>

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

<span data-ttu-id="f13e4-230">`/transient-disposable`에 있는 `TransientDisposable` 구성 요소로 이동하면 프레임워크가 `TransientDisposable`의 인스턴스를 생성하려고 할 때 <xref:System.InvalidOperationException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-230">Navigate to the `TransientDisposable` component at `/transient-disposable` and an <xref:System.InvalidOperationException> is thrown when the framework attempts to construct an instance of `TransientDisposable`:</span></span>

> <span data-ttu-id="f13e4-231">System.InvalidOperationException: 잘못된 범위에서 삭제 가능한 임시 서비스 TransientDisposable을 확인하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-231">System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span></span> <span data-ttu-id="f13e4-232">확인하려는 서비스 'T'에 'OwningComponentBase\<T>' 구성 요소 기본 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f13e4-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f13e4-233">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f13e4-233">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="f13e4-234">임시 및 공유 인스턴스에 대한 `IDisposable` 지침</span><span class="sxs-lookup"><span data-stu-id="f13e4-234">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
