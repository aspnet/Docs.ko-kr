---
title: ASP.NET Core Blazor 종속성 주입
author: guardrex
description: Blazor 앱이 구성 요소에 서비스를 주입할 수 있는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
ms.openlocfilehash: c68deb5237754872e11bfd9c83275b9a3b147319
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556517"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="1f20d-103">ASP.NET Core Blazor 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="1f20d-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="1f20d-104">작성자: [Rainer Stropek](https://www.timecockpit.com) 및 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="1f20d-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="1f20d-105">Blazor는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1f20d-106">앱은 기본 제공 서비스를 구성 요소에 삽입하여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="1f20d-107">앱은 사용자 지정 서비스를 정의 및 등록하고 DI를 통해 앱 전체에서 사용하도록 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="1f20d-108">DI는 중앙 위치에 구성된 서비스에 액세스하기 위한 기술입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="1f20d-109">이는 Blazor 앱에서 다음과 같은 경우에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="1f20d-110">여러 구성 요소에서 *singleton* 서비스라고 하는 단일 서비스 클래스 인스턴스를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="1f20d-111">참조 추상화를 사용하여 구체적인 서비스 클래스의 구성 요소를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="1f20d-112">예를 들어, 앱의 데이터에 액세스하기 위한 `IDataAccess` 인터페이스를 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="1f20d-113">이 인터페이스는 구체적 `DataAccess` 클래스에 의해 구현되고 앱의 서비스 컨테이너에 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="1f20d-114">구성 요소는 DI를 사용하여 `IDataAccess` 구현을 수신하는 경우 구체적인 형식에 결합되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="1f20d-115">단위 테스트의 모의 구현을 위해서는 이 구현을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="1f20d-116">기본 서비스</span><span class="sxs-lookup"><span data-stu-id="1f20d-116">Default services</span></span>

<span data-ttu-id="1f20d-117">기본 서비스는 앱의 서비스 컬렉션에 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="1f20d-118">서비스</span><span class="sxs-lookup"><span data-stu-id="1f20d-118">Service</span></span> | <span data-ttu-id="1f20d-119">수명</span><span class="sxs-lookup"><span data-stu-id="1f20d-119">Lifetime</span></span> | <span data-ttu-id="1f20d-120">설명</span><span class="sxs-lookup"><span data-stu-id="1f20d-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="1f20d-121">Scoped</span><span class="sxs-lookup"><span data-stu-id="1f20d-121">Scoped</span></span> | <span data-ttu-id="1f20d-122">URI로 식별되는 리소스에서 HTTP 요청을 보내고 HTTP 응답을 받기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="1f20d-123">Blazor WebAssembly 앱의 <xref:System.Net.Http.HttpClient> 인스턴스는 브라우저를 사용하여 백그라운드에서 HTTP 트래픽을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="1f20d-124">Blazor Server 앱에는 기본적으로 서비스로 구성된 <xref:System.Net.Http.HttpClient>는 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="1f20d-125">Blazor Server 앱에 <xref:System.Net.Http.HttpClient>를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="1f20d-126">자세한 내용은 <xref:blazor/call-web-api>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="1f20d-127"><xref:System.Net.Http.HttpClient>가 싱글톤이 아닌 범위가 지정된 서비스로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="1f20d-128">자세한 내용은 [서비스 수명](#service-lifetime) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="1f20d-129">singleton(Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="1f20d-129">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="1f20d-130">범위 지정됨(Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="1f20d-130">Scoped (Blazor Server)</span></span> | <span data-ttu-id="1f20d-131">JavaScript 호출이 디스패치되는 JavaScript 런타임의 인스턴스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="1f20d-132">자세한 내용은 <xref:blazor/call-javascript-from-dotnet>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="1f20d-133">singleton(Blazor WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="1f20d-133">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="1f20d-134">범위 지정됨(Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="1f20d-134">Scoped (Blazor Server)</span></span> | <span data-ttu-id="1f20d-135">URI 및 탐색 상태를 사용하기 위한 도우미를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="1f20d-136">자세한 내용은 [URI 및 탐색 상태 도우미](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="1f20d-137">사용자 지정 서비스 공급자는 테이블에 나열된 기본 서비스를 자동으로 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="1f20d-138">사용자 지정 서비스 공급자를 사용하고 표에 표시된 서비스가 필요한 경우 새 서비스 공급자에 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="1f20d-139">앱에 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="1f20d-139">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="1f20d-140">`Program.cs`의 `Main` 메서드에서 앱의 서비스 컬렉션용 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="1f20d-141">다음 예제에서는 `MyDependency` 구현을 `IMyDependency`에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<IMyDependency, MyDependency>();

        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="1f20d-142">일단 호스트를 빌드하면 구성 요소를 렌더링하기 전에 루트 DI 범위에서 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="1f20d-143">이러한 기능은 콘텐츠를 렌더링하기 전에 초기화 논리를 실행하는 데 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-143">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

        builder.Services.AddSingleton<WeatherService>();

        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="1f20d-144">또한 호스트는 앱에 대한 중앙 구성 인스턴스도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="1f20d-145">이전 예제를 기준으로 작성한 날씨 서비스 URL은 기본 구성 원본(예: `appsettings.json`)에서 `InitializeWeatherAsync`로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);

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

### Blazor Server

<span data-ttu-id="1f20d-146">새 앱을 만든 후 `Startup.ConfigureServices` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="1f20d-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> 메서드는 서비스 설명자 개체(<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) 목록에 해당하는 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="1f20d-148">서비스 설명자를 서비스 컬렉션에 제공함으로써 서비스가 `ConfigureServices` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="1f20d-149">다음 예제에서는 `IDataAccess` 인터페이스와 해당 구체적 구현 `DataAccess`를 통해 이러한 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="1f20d-150">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="1f20d-150">Service lifetime</span></span>

<span data-ttu-id="1f20d-151">다음 표에 표시된 수명으로 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="1f20d-152">수명</span><span class="sxs-lookup"><span data-stu-id="1f20d-152">Lifetime</span></span> | <span data-ttu-id="1f20d-153">설명</span><span class="sxs-lookup"><span data-stu-id="1f20d-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="1f20d-154">Blazor WebAssembly 앱에는 현재, DI 범위에 대한 개념이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-154">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="1f20d-155">`Scoped` 등록 서비스는 `Singleton` 서비스처럼 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-155">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="1f20d-156">Blazor Server 호스팅 모델은 HTTP 요청에서 `Scoped` 수명을 지원하지만 클라이언트에서 로드되는 구성 요소 간의 SignalR 연결/회로 메시지에서는 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-156">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="1f20d-157">Razor Pages 또는 앱의 MVC 부분은 범위가 지정된 서비스를 정상적으로 처리하고, 페이지 또는 뷰 사이를 이동하거나 페이지 또는 뷰에서 구성 요소로 이동할 때 각 HTTP 요청에서 서비스를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-157">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="1f20d-158">클라이언트의 구성 요소 사이를 이동할 때는 범위가 지정된 서비스가 다시 구성되지 않습니다. 이 경우 서버와의 통신은 HTTP 요청을 통하지 않고 사용자 회로의 SignalR 연결을 통해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-158">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="1f20d-159">클라이언트에서 다음과 같은 구성 요소 시나리오에서는 사용자를 위해 새 회로가 만들어지므로 범위가 지정된 서비스가 다시 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-159">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="1f20d-160">사용자가 브라우저의 창을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-160">The user closes the browser's window.</span></span> <span data-ttu-id="1f20d-161">사용자가 새 창을 열고 앱으로 다시 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-161">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="1f20d-162">사용자가 브라우저 창에서 앱의 마지막 탭을 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-162">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="1f20d-163">사용자가 새 탭을 열고 앱으로 다시 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-163">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="1f20d-164">사용자가 브라우저의 다시 로드/새로 고침 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-164">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="1f20d-165">Blazor Server 앱의 범위가 지정된 서비스에서 사용자 상태를 보존하는 방법에 대한 자세한 내용은 <xref:blazor/hosting-models?pivots=server>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-165">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="1f20d-166">DI는 서비스의 *단일 인스턴스* 를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-166">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="1f20d-167">`Singleton` 서비스가 필요한 모든 구성 요소는 동일한 서비스의 인스턴스를 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-167">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="1f20d-168">구성 요소는 서비스 컨테이너에서 `Transient` 서비스의 인스턴스를 가져올 때마다 서비스의 *새 인스턴스* 을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-168">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="1f20d-169">DI 시스템은 ASP.NET Core에서 DI 시스템을 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-169">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="1f20d-170">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-170">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="1f20d-171">구성 요소에서 서비스 요청</span><span class="sxs-lookup"><span data-stu-id="1f20d-171">Request a service in a component</span></span>

<span data-ttu-id="1f20d-172">서비스 컬렉션에 서비스를 추가한 후에는 [\@inject](xref:mvc/views/razor#inject) Razor 지시문을 사용하여 서비스를 구성 요소에 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-172">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="1f20d-173">[`@inject`](xref:mvc/views/razor#inject)에는 다음 두 개의 매개 변수가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-173">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="1f20d-174">유형: 주입할 서비스의 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-174">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="1f20d-175">속성: 주입된 앱 서비스를 받는 속성의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-175">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="1f20d-176">이 속성은 수동으로 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-176">The property doesn't require manual creation.</span></span> <span data-ttu-id="1f20d-177">컴파일러에서 속성을 만들기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-177">The compiler creates the property.</span></span>

<span data-ttu-id="1f20d-178">자세한 내용은 <xref:mvc/views/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-178">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="1f20d-179">여러 [`@inject`](xref:mvc/views/razor#inject) 문을 사용하여 여러 서비스를 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-179">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="1f20d-180">다음 예제에서는 [`@inject`](xref:mvc/views/razor#inject)를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-180">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="1f20d-181">`Services.IDataAccess`를 구현하는 서비스는 구성 요소의 속성 `DataRepository`에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-181">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="1f20d-182">코드가 `IDataAccess` 추상화만 사용하는 방식에 유의하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-182">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="1f20d-183">내부적으로 생성된 속성(`DataRepository`)은 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-183">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="1f20d-184">일반적으로 이 특성은 직접 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-184">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="1f20d-185">구성 요소에 기본 클래스가 필요하고 주입된 속성이 기본 클래스에도 필요하면 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성을 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-185">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="1f20d-186">기본 클래스에서 파생된 구성 요소에서는 [`@inject`](xref:mvc/views/razor#inject) 지시문이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-186">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="1f20d-187">기본 클래스의 <xref:Microsoft.AspNetCore.Components.InjectAttribute>만 있으면 충분합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-187">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="1f20d-188">서비스에서 DI 사용</span><span class="sxs-lookup"><span data-stu-id="1f20d-188">Use DI in services</span></span>

<span data-ttu-id="1f20d-189">복잡한 서비스에는 추가 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-189">Complex services might require additional services.</span></span> <span data-ttu-id="1f20d-190">이전 예제에서는 `DataAccess`에 <xref:System.Net.Http.HttpClient> 기본 서비스가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-190">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="1f20d-191">서비스에서는 [`@inject`](xref:mvc/views/razor#inject)(또는 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성)를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-191">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="1f20d-192">대신 *생성자 주입* 을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-192">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="1f20d-193">서비스의 생성자에 매개 변수를 추가하여 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-193">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="1f20d-194">DI는 서비스를 만들 때 생성자에 필요한 서비스를 인식하고 적절히 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-194">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="1f20d-195">다음 예제에서 생성자는 DI를 통해 <xref:System.Net.Http.HttpClient>를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-195">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="1f20d-196"><xref:System.Net.Http.HttpClient>는 기본 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-196"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="1f20d-197">생성자 주입의 필수 조건:</span><span class="sxs-lookup"><span data-stu-id="1f20d-197">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="1f20d-198">모든 인수를 DI에서 처리할 수 있는 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-198">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="1f20d-199">DI에서 다루지 않는 추가 매개 변수는 기본값이 지정되면 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-199">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="1f20d-200">적용 가능한 생성자는 `public`이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-200">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="1f20d-201">적용 가능한 생성자가 하나 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-201">One applicable constructor must exist.</span></span> <span data-ttu-id="1f20d-202">모호한 경우 시 DI는 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-202">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="1f20d-203">DI 범위를 관리 하는 유틸리티 기본 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="1f20d-203">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="1f20d-204">ASP.NET Core 앱에서 범위가 지정된 서비스는 일반적으로 현재 요청으로 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-204">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="1f20d-205">요청이 완료된 후에는 모든 범위 지정 또는 임시 서비스가 DI 시스템에서 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-205">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="1f20d-206">Blazor Server 앱에서 요청 범위는 클라이언트 연결 기간에 지속되므로 임시 및 범위가 지정된 서비스가 예상보다 훨씬 오래 지속될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-206">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="1f20d-207">Blazor WebAssembly 앱에서, 범위가 지정된 수명으로 등록된 서비스는 singleton으로 처리되므로 일반적인 ASP.NET Core 앱의 범위가 지정된 서비스보다 오래 지속됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-207">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="1f20d-208">앱에서 삭제 가능한 임시 서비스를 검색하려면 [임시 삭제 가능 항목 검색](#detect-transient-disposables) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-208">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="1f20d-209">Blazor 앱에서 서비스 수명을 제한하는 방법은 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-209">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="1f20d-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 구성 요소의 수명에 해당하는 DI 범위를 만드는 <xref:Microsoft.AspNetCore.Components.ComponentBase>에서 파생된 추상 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="1f20d-211">이 범위를 사용하는 경우 수명 범위를 지정한 DI 서비스를 사용하고 해당 구성 요소만큼 지속되도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-211">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="1f20d-212">구성 요소가 제거되면 구성 요소 범위 지정 서비스 공급자의 서비스도 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-212">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="1f20d-213">이 기능은 다음과 같은 서비스에 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-213">This can be useful for services that:</span></span>

* <span data-ttu-id="1f20d-214">일시적 수명은 부적절하므로 구성 요소 내에서 다시 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-214">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="1f20d-215">싱글톤 수명은 부적절하므로 구성 요소에서 공유할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-215">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="1f20d-216">두 가지 버전의 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 형식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-216">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="1f20d-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase>는 <xref:System.IServiceProvider> 형식의 보호된 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용하여 <xref:Microsoft.AspNetCore.Components.ComponentBase> 형식의 삭제 가능한 추상 자식입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="1f20d-218">이 공급자는 구성 요소의 수명으로 범위가 지정된 서비스를 확인하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-218">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="1f20d-219">[`@inject`](xref:mvc/views/razor#inject) 또는 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 특성을 사용하여 구성 요소에 주입된 DI 서비스는 구성 요소의 범위에서 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-219">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="1f20d-220">구성 요소의 범위를 사용하려면 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 또는 <xref:System.IServiceProvider.GetService%2A>를 사용하여 서비스를 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-220">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="1f20d-221"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 공급자를 사용하여 확인된 모든 서비스에는 동일한 범위에서 종속성이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-221">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <span data-ttu-id="1f20d-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>에서 파생되고 범위가 지정된 DI 공급자에서 `T`의 인스턴스를 반환하는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-222"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="1f20d-223">이 형식은 구성 요소의 범위를 사용하여 DI 컨테이너에서 앱에 필요한 기본 서비스가 하나 있는 경우 <xref:System.IServiceProvider> 인스턴스를 사용하지 않고 범위 지정 서비스에 액세스할 수 있는 편리한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-223">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="1f20d-224"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 속성을 사용할 수 있으므로 필요한 경우 앱에서 다른 형식의 서비스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-224">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="1f20d-225">DI에서 EF Core(Entity Framework Core) DbContext 사용</span><span class="sxs-lookup"><span data-stu-id="1f20d-225">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="1f20d-226">자세한 내용은 <xref:blazor/blazor-server-ef-core>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-226">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="1f20d-227">임시 삭제 가능 항목 검색</span><span class="sxs-lookup"><span data-stu-id="1f20d-227">Detect transient disposables</span></span>

<span data-ttu-id="1f20d-228">다음 예제에서는 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>를 사용해야 하는 앱에서 삭제 가능한 임시 서비스를 검색하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1f20d-228">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="1f20d-229">자세한 내용은 [DI 범위를 관리하는 유틸리티 기본 구성 요소 클래스](#utility-base-component-classes-to-manage-a-di-scope) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1f20d-229">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="1f20d-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="1f20d-230">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="1f20d-231">다음 예제에서 `TransientDisposable`이 검색되었습니다(`Program.cs`).</span><span class="sxs-lookup"><span data-stu-id="1f20d-231">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

::: moniker-end

### Blazor Server

<span data-ttu-id="1f20d-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="1f20d-232">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="1f20d-233">`Program`:</span><span class="sxs-lookup"><span data-stu-id="1f20d-233">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="1f20d-234">다음 예제에서 `TransientDependency`가 검색되었습니다(`Startup.cs`).</span><span class="sxs-lookup"><span data-stu-id="1f20d-234">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="1f20d-235">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1f20d-235">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="1f20d-236">임시 및 공유 인스턴스에 대한 `IDisposable` 지침</span><span class="sxs-lookup"><span data-stu-id="1f20d-236">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
