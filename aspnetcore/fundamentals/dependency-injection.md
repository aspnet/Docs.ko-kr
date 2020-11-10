---
title: ASP.NET Core에서 종속성 주입
author: rick-anderson
description: ASP.NET Core에서 종속성 주입을 구현하는 방법 및 사용 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 31db9aea9e0b7ed21cae2f87fbb9e2e649782697
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234467"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="02a46-103">ASP.NET Core에서 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="02a46-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02a46-104">작성자: [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) 및 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="02a46-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="02a46-105">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="02a46-106">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="02a46-107">웹앱이 아닌 애플리케이션에서 종속성 주입을 사용하는 방법에 대한 자세한 내용은 [.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="02a46-108">옵션의 종속성 주입에 대한 자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="02a46-109">이 항목에서는 ASP.NET Core의 종속성 삽입에 관한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="02a46-110">종속성 주입 사용에 대한 기본 설명서는 [.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="02a46-111">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02a46-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="02a46-112">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="02a46-112">Overview of dependency injection</span></span>

<span data-ttu-id="02a46-113">‘종속성’은 다른 개체가 종속된 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="02a46-114">다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="02a46-115">클래스에서 `MyDependency` 클래스의 인스턴스를 만들어 `WriteMessage` 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="02a46-116">다음 예제에서 `MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="02a46-117">이 클래스는 `MyDependency` 클래스를 만들고 이 클래스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="02a46-118">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="02a46-119">`MyDependency`를 다른 구현으로 바꾸려면 `IndexModel` 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="02a46-120">`MyDependency`에 종속성이 있으면 `IndexModel` 클래스에서 해당 종속성도 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="02a46-121">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="02a46-122">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="02a46-123">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="02a46-124">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="02a46-125">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="02a46-126">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="02a46-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="02a46-127">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="02a46-128">서비스는 일반적으로 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="02a46-129">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="02a46-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="02a46-130">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="02a46-131">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 `WriteMessage` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="02a46-132">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="02a46-133">샘플 앱에서는 `IMyDependency` 서비스를 구체적인 `MyDependency` 형식으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="02a46-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> 메서드는 단일 요청의 수명인 범위가 지정된 수명으로 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="02a46-135">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="02a46-136">샘플 앱에서는 `IMyDependency` 서비스가 요청되며 이 서비스는 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="02a46-137">DI 패턴을 사용하여 컨트롤러는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="02a46-138">컨트롤러는 구체적인 형식 `MyDependency`를 사용하지 않고 구현되는 `IMyDependency` 인터페이스만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="02a46-139">따라서 컨트롤러에서 사용하는 구현을 컨트롤러를 수정하지 않고 쉽게 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="02a46-140">`MyDependency`의 인스턴스를 만들지 않습니다. 해당 인스턴스는 DI 컨테이너에 의해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="02a46-141">`IMyDependency` 인터페이스의 구현을 기본 제공 로깅 API를 사용하여 향상할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="02a46-142">업데이트된 `ConfigureServices` 메서드는 새 `IMyDependency` 구현을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="02a46-143">`MyDependency2`는 생성자에서 요청하는 <xref:Microsoft.Extensions.Logging.ILogger%601>에 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="02a46-144">`ILogger<TCategoryName>`은 [프레임워크에서 제공하는 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="02a46-145">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="02a46-146">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="02a46-147">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="02a46-148">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="02a46-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="02a46-149">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="02a46-150">종속성 주입 용어에서 서비스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="02a46-151">일반적으로 다른 개체에 `IMyDependency` 서비스와 같은 서비스를 제공하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="02a46-152">서비스에서 웹 서비스를 사용할 수 있지만 웹 서비스와 관련 있는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="02a46-153">해당 프레임워크는 강력한 [로깅](xref:fundamentals/logging/index) 시스템을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="02a46-154">이전 예제에 표시된 `IMyDependency` 구현은 로깅을 구현하는 것이 아니라 기본 DI를 보여 주기 위해 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="02a46-155">대부분의 앱에서는 로거를 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="02a46-156">다음 코드에서는 `ConfigureServices`에 서비스를 등록하지 않아도 되는 기본 로깅을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="02a46-157">위의 코드를 사용하여 프레임워크에서 [로깅](xref:fundamentals/logging/index)을 제공하므로 `ConfigureServices`를 업데이트할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="02a46-158">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="02a46-158">Services injected into Startup</span></span>

<span data-ttu-id="02a46-159">서비스를 `Startup` 생성자 및 `Startup.Configure` 메서드에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="02a46-160">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="02a46-161">DI 컨테이너에 등록된 모든 서비스를 `Startup.Configure` 메서드에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="02a46-162">자세한 내용은 <xref:fundamentals/startup> 및 [시작 시 구성 액세스](xref:fundamentals/configuration/index#access-configuration-in-startup)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="02a46-163">확장 메서드를 사용하여 서비스 그룹 등록</span><span class="sxs-lookup"><span data-stu-id="02a46-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="02a46-164">ASP.NET Core 프레임워크에서는 관련 서비스 그룹을 등록하는 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="02a46-165">규칙은 단일 `Add{GROUP_NAME}` 확장 메서드를 사용하여 프레임워크 기능에 필요한 모든 서비스를 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="02a46-166">예를 들어 <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> 확장 메서드는 MVC 컨트롤러에 필요한 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="02a46-167">다음 코드는 개별 사용자 계정을 사용하는 Razor Pages 템플릿으로 생성되며 확장 메서드 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="02a46-168">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="02a46-168">Service lifetimes</span></span>

<span data-ttu-id="02a46-169">[.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)의 [서비스 수명](/dotnet/core/extensions/dependency-injection#service-lifetimes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="02a46-170">미들웨어에서 범위 지정 서비스를 사용하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="02a46-171">미들웨어의 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="02a46-172">[생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 사용하면 범위 지정 서비스가 싱글톤처럼 작동하게 하므로 런타임 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="02a46-173">[수명 및 등록 옵션](#lifetime-and-registration-options) 섹션의 샘플에서는 `InvokeAsync` 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="02a46-174">[팩터리 기반 미들웨어](xref:fundamentals/middleware/extensibility)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="02a46-175">이 방법을 사용하여 등록된 미들웨어는 클라이언트를 요청(연결)할 때마다 활성화되어, 범위 지정 서비스를 미들웨어의 `InvokeAsync` 메서드에 삽입할 수 있도록 해줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="02a46-176">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="02a46-177">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="02a46-177">Service registration methods</span></span>

<span data-ttu-id="02a46-178">[.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)의 [서비스 등록 메서드](/dotnet/core/extensions/dependency-injection#service-registration-methods)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="02a46-179">[테스트를 위한 형식을 모의할 때](xref:test/integration-tests#inject-mock-services) 여러 구현을 사용하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="02a46-180">구현 형식만으로 서비스를 등록하는 것은 동일한 구현 및 서비스 형식으로 해당 서비스를 등록하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="02a46-181">따라서 명시적 서비스 형식을 사용하지 않는 메서드를 사용하여 서비스의 여러 구현을 등록할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="02a46-182">이러한 메서드는 서비스의 여러 ‘인스턴스’를 등록할 수 있지만 모두 동일한 ‘구현’ 형식을 사용합니다. </span><span class="sxs-lookup"><span data-stu-id="02a46-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="02a46-183">위의 서비스 등록 메서드 중 하나를 사용하여 동일한 서비스 형식의 여러 서비스 인스턴스를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="02a46-184">다음 예제에서는 `IMyDependency`를 서비스 형식으로 사용하여 `AddSingleton`을 두 번 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="02a46-185">두 번째 `AddSingleton` 호출은 `IMyDependency`로 확인되면 이전 호출을 재정의하고 `IEnumerable<IMyDependency>`를 통해 여러 서비스가 확인되면 이전 호출에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="02a46-186">서비스는 `IEnumerable<{SERVICE}>`를 통해 해결될 때 등록된 순서로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="02a46-187">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="02a46-187">Constructor injection behavior</span></span>

<span data-ttu-id="02a46-188">[.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)의 [생성자 주입 동작](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="02a46-189">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="02a46-189">Entity Framework contexts</span></span>

<span data-ttu-id="02a46-190">기본적으로 Entity Framework 컨텍스트는 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="02a46-191">다른 수명을 사용하려면 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 오버로드를 사용하여 수명을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="02a46-192">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="02a46-193">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="02a46-193">Lifetime and registration options</span></span>

<span data-ttu-id="02a46-194">서비스 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 식별자인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="02a46-195">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="02a46-196">다음 `Operation` 클래스는 위의 모든 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="02a46-197">`Operation` 생성자는 GUID를 생성하고 마지막 4자를 `OperationId` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="02a46-198">`Startup.ConfigureServices` 메서드는 명명된 수명에 따라 `Operation` 클래스의 여러 등록을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="02a46-199">샘플 앱에서는 요청 내의 개체 수명과 요청 간의 개체 수명을 모두 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="02a46-200">`IndexModel` 및 미들웨어는 각 `IOperation` 형식을 요청하고 각각에 대한 `OperationId`를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="02a46-201">`IndexModel`과 유사한 미들웨어는 동일한 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="02a46-202">범위가 지정된 서비스는 `InvokeAsync` 메서드에서 해결해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="02a46-203">로거 출력은 다음을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-203">The logger output shows:</span></span>

* <span data-ttu-id="02a46-204">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-204">*Transient* objects are always different.</span></span> <span data-ttu-id="02a46-205">임시 `OperationId` 값은 `IndexModel`과 미들웨어에서 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="02a46-206">‘범위가 지정된’ 개체는 각 요청에 대해 동일하지만 각 요청 간에 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="02a46-207">‘싱글톤’ 개체는 모든 요청에 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="02a46-208">로깅 출력을 줄이려면 *appsettings.Development.json* 파일에서 “Logging:LogLevel:Microsoft:Error”를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="02a46-209">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="02a46-209">Call services from main</span></span>

<span data-ttu-id="02a46-210">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="02a46-211">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="02a46-212">다음 예제에서는 범위가 지정된 `IMyDependency` 서비스에 액세스하고 `Program.Main`에서 해당 `WriteMessage` 메서드를 호출하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="02a46-213">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="02a46-213">Scope validation</span></span>

<span data-ttu-id="02a46-214">[.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)의 [생성자 주입 동작](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="02a46-215">자세한 내용은 [범위 유효성 검사](xref:fundamentals/host/web-host#scope-validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="02a46-216">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="02a46-216">Request Services</span></span>

<span data-ttu-id="02a46-217">ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="02a46-218">요청 내에서 서비스가 요청되는 경우 서비스와 해당 종속성은 `RequestServices` 컬렉션에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="02a46-219">프레임워크는 요청당 범위를 만들고 `RequestServices`는 범위가 지정된 서비스 공급자를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="02a46-220">범위가 지정된 모든 서비스는 요청이 활성 상태인 동안 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="02a46-221">`RequestServices` 컬렉션에 서비스를 확인하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="02a46-222">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="02a46-223">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="02a46-223">Design services for dependency injection</span></span>

<span data-ttu-id="02a46-224">종속성 주입을 위한 서비스를 디자인하는 경우</span><span class="sxs-lookup"><span data-stu-id="02a46-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="02a46-225">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="02a46-226">대신 싱글톤 서비스를 사용하도록 앱을 설계하여 전역 상태를 만들지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="02a46-227">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="02a46-228">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="02a46-229">서비스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="02a46-230">클래스에 주입된 종속성이 많은 경우 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="02a46-231">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="02a46-232">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-232">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="02a46-233">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="02a46-233">Disposal of services</span></span>

<span data-ttu-id="02a46-234">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="02a46-235">개발자는 컨테이너에서 확인된 서비스는 삭제해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="02a46-236">형식 또는 팩터리가 싱글톤으로 등록된 경우 컨테이너에서 싱글톤을 자동으로 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="02a46-237">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="02a46-238">디버그 콘솔에는 인덱스 페이지를 새로 고칠 때마다 다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="02a46-239">서비스 컨테이너에서 만들지 않은 서비스</span><span class="sxs-lookup"><span data-stu-id="02a46-239">Services not created by the service container</span></span>

<span data-ttu-id="02a46-240">다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="02a46-241">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="02a46-241">In the preceding code:</span></span>

* <span data-ttu-id="02a46-242">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="02a46-243">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="02a46-244">개발자가 서비스 삭제를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="02a46-245">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="02a46-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="02a46-246">[.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)의 [임시 및 공유 인스턴스에 대한 IDisposable 지침](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="02a46-247">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="02a46-247">Default service container replacement</span></span>

<span data-ttu-id="02a46-248">[.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)의 [기본 서비스 컨테이너 바꾸기](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="02a46-249">권장 사항</span><span class="sxs-lookup"><span data-stu-id="02a46-249">Recommendations</span></span>

<span data-ttu-id="02a46-250">[.NET의 종속성 주입](/dotnet/core/extensions/dependency-injection)의 [권장 사항](/dotnet/core/extensions/dependency-injection-guidelines#recommendations)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="02a46-251">‘서비스 로케이터 패턴’을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="02a46-252">예를 들어 DI를 대신 사용할 수 있는 경우 서비스 인스턴스를 가져오기 위해 <xref:System.IServiceProvider.GetService%2A>를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="02a46-253">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="02a46-253">**Incorrect:**</span></span>

    ![잘못된 코드](dependency-injection/_static/bad.png)

  <span data-ttu-id="02a46-255">**올바른 예** :</span><span class="sxs-lookup"><span data-stu-id="02a46-255">**Correct** :</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="02a46-256">피해야 하는 또 다른 서비스 로케이터 변형은 런타임에 종속성을 해결하는 팩터리를 주입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="02a46-257">이러한 두 가지 방법 모두 [제어 반전](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="02a46-258">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="02a46-259">`ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A>를 호출하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="02a46-260">일반적으로 `BuildServiceProvider`는 개발자가 `ConfigureServices`에서 서비스를 해결하려는 경우 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="02a46-261">구성에서 `LoginPath`를 로드하는 경우를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="02a46-262">다음 방법을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-262">Avoid the following approach:</span></span>

  ![BuildServiceProvider를 호출하는 잘못된 코드](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="02a46-264">위 이미지에서 `services.BuildServiceProvider` 아래의 녹색 물결 모양 줄을 선택하면 다음 ASP0000 경고가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="02a46-265">ASP0000 애플리케이션 코드에서 ‘BuildServiceProvider’를 호출하면 싱글톤 서비스의 추가 복사본이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="02a46-266">‘Configure’의 매개 변수로 종속성 주입 서비스와 같은 다른 방법을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="02a46-267">`BuildServiceProvider`를 호출하면 두 번째 컨테이너가 만들어지므로, 조각난 싱글톤이 생성되고 여러 컨테이너의 개체 그래프를 참조할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="02a46-268">`LoginPath`를 가져오는 올바른 방법은 DI에 대한 옵션 패턴의 기본 제공 지원을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="02a46-269">삭제 가능한 임시 서비스는 삭제를 위해 컨테이너에서 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="02a46-270">따라서 최상위 컨테이너에서 해결할 경우 메모리 누수가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="02a46-271">범위 유효성 검사를 사용하여 범위가 지정된 서비스를 캡처하는 싱글톤이 앱에 없는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="02a46-272">자세한 내용은 [범위 유효성 검사](#scope-validation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="02a46-273">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="02a46-274">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="02a46-275">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="02a46-276">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="02a46-277">DI의 다중 테넌트에 권장되는 패턴</span><span class="sxs-lookup"><span data-stu-id="02a46-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="02a46-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore)는 ASP.NET Core에서 모듈식 다중 테넌트 애플리케이션을 빌드하기 위한 애플리케이션 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="02a46-279">자세한 내용은 [Orchard Core 설명서](https://docs.orchardcore.net/en/dev/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="02a46-280">CMS 고유 기능 없이 Orchard Core Framework를 사용하여 모듈식 다중 테넌트 앱을 빌드하는 방법에 대한 예제는 [Orchard Core 샘플](https://github.com/OrchardCMS/OrchardCore.Samples)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="02a46-281">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="02a46-281">Framework-provided services</span></span>

<span data-ttu-id="02a46-282">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="02a46-283">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="02a46-284">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에서 250개 이상의 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="02a46-285">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="02a46-286">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="02a46-286">Service Type</span></span>                                                                                    | <span data-ttu-id="02a46-287">수명</span><span class="sxs-lookup"><span data-stu-id="02a46-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="02a46-288">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="02a46-289">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="02a46-290">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="02a46-291">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="02a46-292">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="02a46-293">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="02a46-294">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="02a46-295">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="02a46-296">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="02a46-297">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="02a46-298">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="02a46-299">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="02a46-300">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="02a46-301">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="02a46-302">추가 자료</span><span class="sxs-lookup"><span data-stu-id="02a46-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="02a46-303">DI 앱 개발을 위한 NDC 컨퍼런스 패턴</span><span class="sxs-lookup"><span data-stu-id="02a46-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="02a46-304">IDisposables in ASP.NET Core를 삭제하는 네 가지 방법</span><span class="sxs-lookup"><span data-stu-id="02a46-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="02a46-305">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="02a46-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="02a46-306">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="02a46-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="02a46-307">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="02a46-307">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="02a46-308">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="02a46-308">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02a46-309">작성자: [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) 및 [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="02a46-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="02a46-310">ASP.NET Core는 클래스와 해당 종속성 간의 [IoC(Inversion of Control)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)를 실현하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="02a46-311">MVC 컨트롤러 내의 종속성 주입에 대한 자세한 내용은 <xref:mvc/controllers/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="02a46-312">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02a46-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="02a46-313">종속성 주입 개요</span><span class="sxs-lookup"><span data-stu-id="02a46-313">Overview of dependency injection</span></span>

<span data-ttu-id="02a46-314">‘종속성’은 다른 개체에 필요한 모든 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="02a46-315">앱의 다른 클래스가 종속된 `MyDependency` 클래스에서 `WriteMessage` 메서드를 사용하는 다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="02a46-316">`MyDependency` 클래스의 인스턴스를 만들어 클래스에서 `WriteMessage` 메서드를 사용할 수 있게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="02a46-317">`MyDependency` 클래스는 `IndexModel` 클래스의 종속성입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="02a46-318">이 클래스는 `MyDependency` 인스턴스를 만들고 이 인스턴스에 직접 종속됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="02a46-319">이전 예제와 같은 코드 종속성은 문제가 있으며 다음과 같은 이유로 사용하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="02a46-320">`MyDependency`를 다른 구현으로 바꾸려면 클래스를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="02a46-321">`MyDependency`에 종속성이 있으면 클래스에서 해당 종속성을 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="02a46-322">여러 클래스가 `MyDependency`에 종속되어 있는 대형 프로젝트에서는 구성 코드가 앱 전체에 분산됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="02a46-323">이 구현은 단위 테스트하기가 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="02a46-324">앱에서 모의 또는 스텁 `MyDependency` 클래스를 사용해야 하지만, 이 방법에서는 가능하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="02a46-325">종속성 주입은 다음을 통해 이러한 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="02a46-326">인퍼테이스 또는 기본 클래스를 사용하여 종속성 구현을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="02a46-327">서비스 컨테이너에 종속성 등록.</span><span class="sxs-lookup"><span data-stu-id="02a46-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="02a46-328">ASP.NET Core는 서비스 컨테이너인 <xref:System.IServiceProvider>를 기본 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="02a46-329">서비스는 앱의 `Startup.ConfigureServices` 메서드에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="02a46-330">서비스가 사용되는 클래스의 생성자에 주입됨.</span><span class="sxs-lookup"><span data-stu-id="02a46-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="02a46-331">프레임워크가 종속성의 인스턴스를 만들고 더 이상 필요하지 않으면 삭제하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="02a46-332">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)에서 `IMyDependency` 인터페이스는 서비스가 앱에 제공하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="02a46-333">이 인터페이스는 구체적인 형식 `MyDependency`에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="02a46-334">`MyDependency`는 자신의 생성자에서 <xref:Microsoft.Extensions.Logging.ILogger`1>를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="02a46-335">종속성 주입을 연결된 방식으로 사용하는 일은 특별한 경우가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="02a46-336">요청된 각 종속성은 차례로 자체 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="02a46-337">컨테이너는 그래프의 종속성을 해결하고 완전히 해결된 서비스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="02a46-338">해결해야 하는 종속성이 모인 집합은 일반적으로 종속성 트리, 종속성 그래프 또는 개체 그래프라고 합니다  .</span><span class="sxs-lookup"><span data-stu-id="02a46-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="02a46-339">`IMyDependency` 및 `ILogger<TCategoryName>`는 서비스 컨테이너에 등록되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="02a46-340">`IMyDependency`는 `Startup.ConfigureServices`에서 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="02a46-341">`ILogger<TCategoryName>`은 로깅 추상화 인프라에서 등록하므로, 프레임워크에서 기본적으로 등록한 [프레임워크 제공 서비스](#framework-provided-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="02a46-342">컨테이너는 [개방형 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)을 활용하여 `ILogger<TCategoryName>`을 해결하므로 모든 [생성된 형식(제네릭)](/dotnet/csharp/language-reference/language-specification/types#constructed-types)을 등록하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="02a46-343">샘플 앱에서 `IMyDependency` 서비스는 구체적인 형식 `MyDependency`로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="02a46-344">이 등록은 서비스 수명을 단일 요청의 수명으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="02a46-345">[서비스 수명](#service-lifetimes)에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="02a46-346">각 `services.Add{SERVICE_NAME}` 확장 메서드는 서비스를 추가(및 잠재적으로 구성)합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-346">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="02a46-347">예를 들어 `services.AddMvc()`는 Razor Pages와 MVC에서 요청하는 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-347">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="02a46-348">앱에서 이 규칙을 따르는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="02a46-349">확장 메서드를 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 네임스페이스에 배치하여 서비스 등록 그룹을 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-349">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="02a46-350">서비스의 생성자에 `string`과 같은 [기본 제공](/dotnet/csharp/language-reference/keywords/built-in-types-table) 형식이 필요한 경우 [구성](xref:fundamentals/configuration/index) 및 [옵션 패턴](xref:fundamentals/configuration/options)을 사용하여 해당 형식을 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-350">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="02a46-351">서비스의 인스턴스는 서비스가 사용되는 클래스의 생성자를 통해 요청되고 전용 필드에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-351">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="02a46-352">이 필드는 클래스 전체에서 필요에 따라 서비스에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-352">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="02a46-353">샘플 앱에서는 `IMyDependency` 인스턴스가 요청되며 이 인스턴스는 서비스의 `WriteMessage` 메서드를 호출하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-353">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="02a46-354">시작에 삽입된 서비스</span><span class="sxs-lookup"><span data-stu-id="02a46-354">Services injected into Startup</span></span>

<span data-ttu-id="02a46-355">제네릭 호스트(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-355">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="02a46-356">서비스는 `Startup.Configure`에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-356">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="02a46-357">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-357">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="02a46-358">프레임워크에서 제공하는 서비스</span><span class="sxs-lookup"><span data-stu-id="02a46-358">Framework-provided services</span></span>

<span data-ttu-id="02a46-359">`Startup.ConfigureServices` 메서드는 Entity Framework Core 및 ASP.NET Core MVC와 같은 플랫폼 기능을 비롯해 앱이 사용하는 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-359">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="02a46-360">처음에 `ConfigureServices`에 제공되는 `IServiceCollection`에는 [호스트가 구성된 방법](xref:fundamentals/index#host)에 따라 달라지는 프레임워크에 의해 정의되는 서비스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-360">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="02a46-361">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 프레임워크에 의해 등록된 수백 개의 서비스를 가지는 것은 드문 일이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-361">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="02a46-362">다음 표에는 프레임워크에서 등록한 서비스들의 작은 샘플이 나열되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-362">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="02a46-363">서비스 종류</span><span class="sxs-lookup"><span data-stu-id="02a46-363">Service Type</span></span> | <span data-ttu-id="02a46-364">수명</span><span class="sxs-lookup"><span data-stu-id="02a46-364">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="02a46-365">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-365">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="02a46-366">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-366">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="02a46-367">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-367">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="02a46-368">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-368">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="02a46-369">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-369">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="02a46-370">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="02a46-371">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-371">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="02a46-372">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-372">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="02a46-373">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-373">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="02a46-374">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-374">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="02a46-375">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-375">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="02a46-376">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-376">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="02a46-377">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-377">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="02a46-378">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-378">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="02a46-379">확장 메서드를 사용하여 추가 서비스 등록</span><span class="sxs-lookup"><span data-stu-id="02a46-379">Register additional services with extension methods</span></span>

<span data-ttu-id="02a46-380">서비스 컬렉션 확장 메서드를 사용하여 서비스(및 필요한 경우 해당 종속 서비스)를 등록할 수 있는 경우 단일 `Add{SERVICE_NAME}` 확장 메서드를 사용하여 해당 서비스에 필요한 모든 서비스를 등록하는 것이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-380">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="02a46-381">다음 코드는 확장 메서드 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 및 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>를 사용하여 컨테이너에 서비스를 추가하는 방법을 보여 주는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-381">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="02a46-382">자세한 내용은 API 설명서의 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 클래스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-382">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="02a46-383">서비스 수명</span><span class="sxs-lookup"><span data-stu-id="02a46-383">Service lifetimes</span></span>

<span data-ttu-id="02a46-384">등록된 각 서비스의 수명을 적절히 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-384">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="02a46-385">ASP.NET Core 서비스는 다음 수명을 사용하여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-385">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="02a46-386">Transient</span><span class="sxs-lookup"><span data-stu-id="02a46-386">Transient</span></span>

<span data-ttu-id="02a46-387">Transient 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>)는 서비스 컨테이너에서 요청할 때마다 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-387">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="02a46-388">이 수명은 간단한 상태 비저장 서비스에 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-388">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="02a46-389">요청을 처리하는 앱에서 Transient 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-389">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="02a46-390">Scoped</span><span class="sxs-lookup"><span data-stu-id="02a46-390">Scoped</span></span>

<span data-ttu-id="02a46-391">Scoped 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>)는 클라이언트 요청(연결)당 한 번 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-391">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="02a46-392">요청을 처리하는 앱에서 Scoped 서비스는 요청이 끝날 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-392">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="02a46-393">미들웨어에서 범위가 지정된 서비스를 사용하는 경우 `Invoke` 또는 `InvokeAsync` 메서드에 서비스를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-393">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="02a46-394">[생성자 삽입](xref:mvc/controllers/dependency-injection#constructor-injection)은 서비스가 singleton처럼 작동하게 하므로 이러한 방법으로 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-394">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="02a46-395">자세한 내용은 <xref:fundamentals/middleware/write#per-request-middleware-dependencies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-395">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="02a46-396">Singleton</span><span class="sxs-lookup"><span data-stu-id="02a46-396">Singleton</span></span>

<span data-ttu-id="02a46-397">싱글톤 수명 서비스(<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)는 처음 요청할 때(또는 `Startup.ConfigureServices`를 실행하고 서비스 등록에서 인스턴스를 지정하는 경우) 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-397">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="02a46-398">모든 후속 요청에서는 같은 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-398">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="02a46-399">앱에 싱글톤 동작이 필요한 경우 서비스 컨테이너가 서비스 수명을 관리하도록 허용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-399">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="02a46-400">싱글톤 디자인 패턴을 구현하거나 클래스의 개체 수명을 관리하는 사용자 코드를 제공하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-400">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="02a46-401">요청을 처리하는 앱에서 Singleton 서비스는 앱 종료 시 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider>가 삭제될 때 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-401">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="02a46-402">범위가 지정된 서비스를 싱글톤에서 해결하면 위험합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-402">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="02a46-403">이 경우 후속 요청을 처리할 때 서비스가 잘못된 상태일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-403">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="02a46-404">서비스 등록 메서드</span><span class="sxs-lookup"><span data-stu-id="02a46-404">Service registration methods</span></span>

<span data-ttu-id="02a46-405">서비스 등록 확장 메서드는 특정 시나리오에 유용한 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-405">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="02a46-406">방법</span><span class="sxs-lookup"><span data-stu-id="02a46-406">Method</span></span> | <span data-ttu-id="02a46-407">자동</span><span class="sxs-lookup"><span data-stu-id="02a46-407">Automatic</span></span><br><span data-ttu-id="02a46-408">개체</span><span class="sxs-lookup"><span data-stu-id="02a46-408">object</span></span><br><span data-ttu-id="02a46-409">삭제</span><span class="sxs-lookup"><span data-stu-id="02a46-409">disposal</span></span> | <span data-ttu-id="02a46-410">여러</span><span class="sxs-lookup"><span data-stu-id="02a46-410">Multiple</span></span><br><span data-ttu-id="02a46-411">구현</span><span class="sxs-lookup"><span data-stu-id="02a46-411">implementations</span></span> | <span data-ttu-id="02a46-412">인수 전달</span><span class="sxs-lookup"><span data-stu-id="02a46-412">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="02a46-413">예제:</span><span class="sxs-lookup"><span data-stu-id="02a46-413">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="02a46-414">예</span><span class="sxs-lookup"><span data-stu-id="02a46-414">Yes</span></span> | <span data-ttu-id="02a46-415">yes</span><span class="sxs-lookup"><span data-stu-id="02a46-415">Yes</span></span> | <span data-ttu-id="02a46-416">예</span><span class="sxs-lookup"><span data-stu-id="02a46-416">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="02a46-417">예제:</span><span class="sxs-lookup"><span data-stu-id="02a46-417">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="02a46-418">예</span><span class="sxs-lookup"><span data-stu-id="02a46-418">Yes</span></span> | <span data-ttu-id="02a46-419">yes</span><span class="sxs-lookup"><span data-stu-id="02a46-419">Yes</span></span> | <span data-ttu-id="02a46-420">예</span><span class="sxs-lookup"><span data-stu-id="02a46-420">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="02a46-421">예제:</span><span class="sxs-lookup"><span data-stu-id="02a46-421">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="02a46-422">예</span><span class="sxs-lookup"><span data-stu-id="02a46-422">Yes</span></span> | <span data-ttu-id="02a46-423">예</span><span class="sxs-lookup"><span data-stu-id="02a46-423">No</span></span> | <span data-ttu-id="02a46-424">예</span><span class="sxs-lookup"><span data-stu-id="02a46-424">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="02a46-425">예제:</span><span class="sxs-lookup"><span data-stu-id="02a46-425">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="02a46-426">예</span><span class="sxs-lookup"><span data-stu-id="02a46-426">No</span></span> | <span data-ttu-id="02a46-427">yes</span><span class="sxs-lookup"><span data-stu-id="02a46-427">Yes</span></span> | <span data-ttu-id="02a46-428">예</span><span class="sxs-lookup"><span data-stu-id="02a46-428">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="02a46-429">예제:</span><span class="sxs-lookup"><span data-stu-id="02a46-429">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="02a46-430">예</span><span class="sxs-lookup"><span data-stu-id="02a46-430">No</span></span> | <span data-ttu-id="02a46-431">예</span><span class="sxs-lookup"><span data-stu-id="02a46-431">No</span></span> | <span data-ttu-id="02a46-432">예</span><span class="sxs-lookup"><span data-stu-id="02a46-432">Yes</span></span> |

<span data-ttu-id="02a46-433">형식 삭제에 대한 자세한 내용은 [서비스의 삭제](#disposal-of-services) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-433">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="02a46-434">여러 구현에 대한 일반적인 시나리오는 [테스트용 모의 형식](xref:test/integration-tests#inject-mock-services)입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-434">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="02a46-435">구현 형식만으로 서비스를 등록하는 것은 동일한 구현 및 서비스 형식으로 해당 서비스를 등록하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-435">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="02a46-436">따라서 명시적 서비스 형식을 사용하지 않는 메서드를 사용하여 서비스의 여러 구현을 등록할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-436">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="02a46-437">이러한 메서드는 서비스의 여러 ‘인스턴스’를 등록할 수 있지만 모두 동일한 ‘구현’ 형식을 사용합니다. </span><span class="sxs-lookup"><span data-stu-id="02a46-437">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="02a46-438">위의 서비스 등록 메서드 중 하나를 사용하여 동일한 서비스 형식의 여러 서비스 인스턴스를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-438">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="02a46-439">다음 예제에서는 `IMyDependency`를 서비스 형식으로 사용하여 `AddSingleton`을 두 번 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-439">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="02a46-440">두 번째 `AddSingleton` 호출은 `IMyDependency`로 확인되면 이전 호출을 재정의하고 `IEnumerable<IMyDependency>`를 통해 여러 서비스가 확인되면 이전 호출에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-440">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="02a46-441">서비스는 `IEnumerable<{SERVICE}>`를 통해 해결될 때 등록된 순서로 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-441">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="02a46-442">또한 프레임워크에서는 아직 등록된 구현이 없는 경우에만 서비스를 등록하는 `TryAdd{LIFETIME}` 확장 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-442">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="02a46-443">다음 예제에서 `AddSingleton`을 호출하면 `MyDependency`가 `IMyDependency`에 대한 구현으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-443">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="02a46-444">`TryAddSingleton` 호출은 `IMyDependency`에 이미 등록된 구현이 있기 때문에 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-444">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="02a46-445">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-445">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="02a46-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 메서드는 *동일한 형식* 의 구현이 아직 없는 경우에만 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="02a46-447">여러 서비스가 `IEnumerable<{SERVICE}>`를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-447">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="02a46-448">서비스를 등록할 때 개발자는 동일한 유형 중 하나가 아직 추가되지 않은 경우에만 인스턴스를 추가하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-448">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="02a46-449">일반적으로 이 메서드는 라이브러리 작성자가 컨테이너에 두 개 복사본을 등록하지 않도록 하기 위해 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-449">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="02a46-450">다음 예제에서 첫 번째 줄은 `IMyDep1`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-450">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="02a46-451">두 번째 줄은 `IMyDep2`에 대한 `MyDep`를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-451">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="02a46-452">세 번째 줄은 `IMyDep1`에 이미 `MyDep`의 등록된 구현이 이미 있으므로 아무런 효과가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-452">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="02a46-453">생성자 주입 동작</span><span class="sxs-lookup"><span data-stu-id="02a46-453">Constructor injection behavior</span></span>

<span data-ttu-id="02a46-454">다음 두 가지 메커니즘으로 서비스를 해결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-454">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="02a46-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: 종속성 주입 컨테이너에 서비스 등록 없이 개체를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="02a46-456">`ActivatorUtilities`는 태그 도우미, MVC 컨트롤러 및 모델 바인더와 같은 사용자용 추상화에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-456">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="02a46-457">생성자에는 종속성 주입으로 제공되지 않는 인수를 사용할 수 있지만, 인수에 기본값을 할당해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-457">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="02a46-458">`IServiceProvider` 또는 `ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)에 *public* 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-458">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="02a46-459">`ActivatorUtilities`로 서비스를 해결하는 경우 [생성자 주입](xref:mvc/controllers/dependency-injection#constructor-injection)을 위해서는 적합한 생성자가 하나만 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-459">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="02a46-460">생성자 오버로드가 지원되지만, 해당 인수가 모두 종속성 주입으로 처리될 수 있는 하나의 오버로드만 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-460">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="02a46-461">Entity Framework 컨텍스트</span><span class="sxs-lookup"><span data-stu-id="02a46-461">Entity Framework contexts</span></span>

<span data-ttu-id="02a46-462">Entity Framework 컨텍스트는 일반적으로 [범위가 지정된 수명](#service-lifetimes)을 사용하여 서비스 컨테이너에 추가됩니다. 이는 웹앱 데이터베이스 작업이 일반적으로 클라이언트 요청에 따라 범위가 지정되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-462">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="02a46-463">데이터베이스 컨텍스트를 등록할 때 [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 오버로드로 수명을 지정하지 않으면 기본 수명으로 수명 범위가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-463">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="02a46-464">지정된 수명의 서비스는 서비스보다 수명이 짧은 데이터베이스 컨텍스트를 사용해서는 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-464">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="02a46-465">수명 및 등록 옵션</span><span class="sxs-lookup"><span data-stu-id="02a46-465">Lifetime and registration options</span></span>

<span data-ttu-id="02a46-466">이러한 수명 및 등록 옵션 간의 차이점을 살펴보려면 작업을 고유한 ID인 `OperationId`가 부여된 작업으로 나타내는 다음 인터페이스들을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-466">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="02a46-467">다음 인터페이스들에 대해 작업 서비스의 수명을 구성하는 방법에 따라 컨테이너는 클래스에서 요청할 때 서비스의 같은 인스턴스나 다른 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-467">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="02a46-468">인터페이스들은 `Operation` 클래스에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-468">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="02a46-469">`Operation` 생성자는 GUID가 제공되지 않는 경우 GUID를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-469">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="02a46-470">`OperationService`는 각각 다른 `Operation` 형식에 종속되어 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-470">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="02a46-471">종속성 주입을 통해 `OperationService`를 요청하면 종속 서비스의 수명에 따라 각 서비스의 새 인스턴스나 기존 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-471">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="02a46-472">컨테이너에서 요청할 때 임시 서비스가 생성되면 `IOperationTransient` 서비스의 `OperationId`는 `OperationService`의 `OperationId`와 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-472">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="02a46-473">`OperationService`는 `IOperationTransient` 클래스의 새 인스턴스를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-473">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="02a46-474">새 인스턴스는 다른 `OperationId`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-474">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="02a46-475">클라이언트 요청에 따라 범위가 지정된 서비스가 생성되면 `IOperationScoped` 서비스의 `OperationId`는 클라이언트 요청 내의 `OperationService`와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-475">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="02a46-476">전체 클라이언트 요청에서 두 서비스는 다른 `OperationId` 값을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-476">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="02a46-477">싱글톤 및 싱글톤 인스턴스 서비스가 한 번 생성되어 모든 클라이언트 요청 및 모든 서비스에서 사용될 경우 `OperationId`는 모든 서비스 요청에서 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-477">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="02a46-478">`Startup.ConfigureServices`에서 각 형식이 자신의 명명된 수명에 따라 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-478">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="02a46-479">`IOperationSingletonInstance` 서비스는 알려진 ID가 `Guid.Empty`인 특정 인스턴스를 사용 중입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-479">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="02a46-480">이 형식이 사용 중임을 명확하게 알 수 있습니다(이 인스턴스의 GUID는 모두 0입니다).</span><span class="sxs-lookup"><span data-stu-id="02a46-480">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="02a46-481">이 샘플 앱은 개별 요청 내의 개체 수명과 개별 요청 간의 개체 수명을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-481">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="02a46-482">샘플 앱의 `IndexModel`은 각 종류의 `IOperation` 형식과 `OperationService`를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-482">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="02a46-483">그런 다음, 페이지에서는 속성 할당을 통해 페이지 모델 클래스 및 서비스의 `OperationId` 값을 모두 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-483">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="02a46-484">다음 출력은 두 요청의 결과를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-484">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="02a46-485">**첫 번째 요청:** :</span><span class="sxs-lookup"><span data-stu-id="02a46-485">**First request:**</span></span>

<span data-ttu-id="02a46-486">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="02a46-486">Controller operations:</span></span>

<span data-ttu-id="02a46-487">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="02a46-487">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="02a46-488">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="02a46-488">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="02a46-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="02a46-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="02a46-490">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="02a46-490">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="02a46-491">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="02a46-491">`OperationService` operations:</span></span>

<span data-ttu-id="02a46-492">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="02a46-492">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="02a46-493">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="02a46-493">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="02a46-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="02a46-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="02a46-495">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="02a46-495">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="02a46-496">**두 번째 요청** :</span><span class="sxs-lookup"><span data-stu-id="02a46-496">**Second request:**</span></span>

<span data-ttu-id="02a46-497">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="02a46-497">Controller operations:</span></span>

<span data-ttu-id="02a46-498">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="02a46-498">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="02a46-499">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="02a46-499">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="02a46-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="02a46-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="02a46-501">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="02a46-501">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="02a46-502">`OperationService` 작업:</span><span class="sxs-lookup"><span data-stu-id="02a46-502">`OperationService` operations:</span></span>

<span data-ttu-id="02a46-503">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="02a46-503">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="02a46-504">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="02a46-504">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="02a46-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="02a46-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="02a46-506">인스턴스: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="02a46-506">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="02a46-507">어떤 `OperationId` 값이 요청 내에서 그리고 요청 간에 달라지는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-507">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="02a46-508">*Transient* 개체는 항상 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-508">*Transient* objects are always different.</span></span> <span data-ttu-id="02a46-509">첫 번째와 두 번째 클라이언트 요청에 대한 임시 `OperationId` 값은 `OperationService` 작업과 클라이언트 요청 간에 모두 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-509">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="02a46-510">각 서비스 요청과 클라이언트 요청에 새 인스턴스가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-510">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="02a46-511">*Scoped* 개체는 클라이언트 요청 내에서는 동일하지만 클라이언트 요청 간에는 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-511">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="02a46-512">*Singleton* 개체는 `Startup.ConfigureServices`에서 `Operation` 인스턴스 제공 여부와 관계없이 모든 개체 및 모든 요청에 대해 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-512">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="02a46-513">Main에서 서비스 호출</span><span class="sxs-lookup"><span data-stu-id="02a46-513">Call services from main</span></span>

<span data-ttu-id="02a46-514">[IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*)를 사용하여 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 만들어서 앱 범위 내로 범위가 지정된 서비스를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-514">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="02a46-515">이 방법은 시작 시 범위가 지정된 서비스에 액세스하여 초기화 작업을 실행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-515">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="02a46-516">다음 예제에서는 `Program.Main`에서 `MyScopedService`에 대한 컨텍스트를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-516">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="02a46-517">범위 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="02a46-517">Scope validation</span></span>

<span data-ttu-id="02a46-518">앱이 개발 환경에서 실행 중인 경우 기본 서비스 공급자가 다음을 확인하는 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-518">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="02a46-519">'직접으로' in current translation is awkward/inappropriate in terms of grammar.</span><span class="sxs-lookup"><span data-stu-id="02a46-519">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="02a46-520">범위가 지정된 서비스는 직접 또는 간접적으로 싱글톤에 삽입되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-520">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="02a46-521">루트 서비스 공급자는 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>를 호출할 때 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-521">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="02a46-522">루트 서비스 공급자의 수명은 공급자가 앱과 함께 시작되고 앱이 종료될 때 삭제되는 앱/서버의 수명에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-522">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="02a46-523">범위가 지정된 서비스는 서비스를 만든 컨테이너에 의해 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-523">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="02a46-524">범위가 지정된 서비스가 루트 컨테이너에서 만들어지는 경우 서비스의 수명은 사실상 싱글톤으로 승격됩니다. 해당 서비스는 앱/서버가 종료될 때 루트 컨테이너에 의해서만 삭제되기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-524">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="02a46-525">서비스 범위의 유효성 검사는 `BuildServiceProvider`가 호출될 경우 이러한 상황을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-525">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="02a46-526">자세한 내용은 <xref:fundamentals/host/web-host#scope-validation>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-526">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="02a46-527">요청 서비스</span><span class="sxs-lookup"><span data-stu-id="02a46-527">Request Services</span></span>

<span data-ttu-id="02a46-528">`HttpContext`의 ASP.NET Core 요청 내에서 사용할 수 있는 서비스는 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 컬렉션을 통해 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-528">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="02a46-529">요청 서비스는 앱의 일부로 구성 및 요청된 서비스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-529">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="02a46-530">개체가 종속성을 지정한 경우에는 `ApplicationServices`가 아닌 `RequestServices`에 있는 형식으로 충족됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-530">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="02a46-531">일반적으로 앱은 이러한 속성을 직접 사용해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-531">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="02a46-532">대신 클래스 생성자를 통해 클래스에 필요한 형식을 요청하고 프레임워크가 종속성을 주입하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-532">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="02a46-533">이 방법이 테스트하기 쉬운 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-533">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="02a46-534">`RequestServices` 컬렉션에 액세스하는 것보다 생성자 매개 변수로 종속성을 요청하는 것을 선호합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-534">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="02a46-535">종속성 주입을 위한 서비스 디자인</span><span class="sxs-lookup"><span data-stu-id="02a46-535">Design services for dependency injection</span></span>

<span data-ttu-id="02a46-536">모범 사례는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-536">Best practices are to:</span></span>

* <span data-ttu-id="02a46-537">종속성 주입을 사용하여 종속성을 가져오도록 서비스를 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-537">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="02a46-538">상태 저장 정적 클래스 및 멤버를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-538">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="02a46-539">대신 singleton 서비스를 사용하여 전역 상태를 만들지 않도록 앱을 디자인합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-539">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="02a46-540">서비스 내의 종속 클래스를 직접 인스턴스화하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-540">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="02a46-541">직접 인스턴스화는 코드를 특정 구현에 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-541">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="02a46-542">앱 클래스를 작고 잘 구성되고 쉽게 테스트할 수 있도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-542">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="02a46-543">클래스에 주입된 종속성이 너무 많아 보이면 일반적으로 클래스에 역할이 너무 많고 [SRP(단일 책임 원칙)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)를 위반하는 것일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-543">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="02a46-544">해당 책임 몇 가지를 새로운 클래스로 이동하여 클래스를 리팩터링해 보세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-544">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="02a46-545">Razor Pages의 페이지 모델 클래스 및 MVC 컨트롤러 클래스는 UI 고려 사항에 집중해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-545">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="02a46-546">비즈니스 규칙 및 데이터 액세스 구현 세부 정보는 이러한 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)에 적합한 클래스에 유지되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-546">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="02a46-547">서비스 삭제</span><span class="sxs-lookup"><span data-stu-id="02a46-547">Disposal of services</span></span>

<span data-ttu-id="02a46-548">컨테이너는 자신이 만든 <xref:System.IDisposable> 형식에 대해 <xref:System.IDisposable.Dispose*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-548">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="02a46-549">인스턴스가 사용자 코드로 컨테이너에 추가된 경우에는 자동으로 삭제되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-549">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="02a46-550">다음 예제에서는 서비스가 서비스 컨테이너에 의해 만들어지고 자동으로 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-550">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="02a46-551">다음 예제에서,</span><span class="sxs-lookup"><span data-stu-id="02a46-551">In the following example:</span></span>

* <span data-ttu-id="02a46-552">서비스 인스턴스가 서비스 컨테이너에 의해 만들어지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-552">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="02a46-553">의도된 서비스 수명을 프레임워크가 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-553">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="02a46-554">프레임워크가 서비스를 자동으로 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-554">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="02a46-555">개발자 코드에서 서비스가 명시적으로 삭제되지 않을 경우 해당 서비스는 앱이 종료될 때까지 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-555">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="02a46-556">임시 및 공유 인스턴스에 대한 IDisposable 지침</span><span class="sxs-lookup"><span data-stu-id="02a46-556">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="02a46-557">임시적인 제한 수명</span><span class="sxs-lookup"><span data-stu-id="02a46-557">Transient, limited lifetime</span></span>

<span data-ttu-id="02a46-558">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="02a46-558">**Scenario**</span></span>

<span data-ttu-id="02a46-559">앱에는 다음 시나리오 중 하나에 대해 임시 수명으로 <xref:System.IDisposable> 인스턴스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-559">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="02a46-560">인스턴스는 루트 범위에서 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-560">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="02a46-561">범위가 끝나기 전에 인스턴스를 삭제해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-561">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="02a46-562">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="02a46-562">**Solution**</span></span>

<span data-ttu-id="02a46-563">부모 범위 밖에서 인스턴스를 생성하려면 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-563">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="02a46-564">이 경우 앱에는 일반적으로 최종 형식의 생성자를 직접 호출하는 `Create` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-564">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="02a46-565">최종 형식에 다른 종속성이 있는 경우 팩터리는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-565">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="02a46-566">해당 생성자에서 <xref:System.IServiceProvider>을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-566">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="02a46-567">해당 종속성에서 컨테이너를 사용하는 동안 컨테이너 외부의 인스턴스를 인스턴스화하기 위해 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-567">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="02a46-568">공유 인스턴스 및 제한 수명</span><span class="sxs-lookup"><span data-stu-id="02a46-568">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="02a46-569">**시나리오**</span><span class="sxs-lookup"><span data-stu-id="02a46-569">**Scenario**</span></span>

<span data-ttu-id="02a46-570">앱은 여러 서비스에서 공유 <xref:System.IDisposable> 인스턴스가 필요하지만 <xref:System.IDisposable>는 수명이 제한되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-570">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="02a46-571">**해결 방법**</span><span class="sxs-lookup"><span data-stu-id="02a46-571">**Solution**</span></span>

<span data-ttu-id="02a46-572">인스턴스를 범위가 지정된 수명으로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-572">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="02a46-573"><xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>를 사용하여 시작하고 새로운 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-573">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="02a46-574">범위의 <xref:System.IServiceProvider>를 사용하여 필요한 서비스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-574">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="02a46-575">수명을 종료해야 하는 경우 범위를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-575">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="02a46-576">일반 지침</span><span class="sxs-lookup"><span data-stu-id="02a46-576">General Guidelines</span></span>

* <span data-ttu-id="02a46-577">임시 범위에 <xref:System.IDisposable> 인스턴스를 등록하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-577">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="02a46-578">대신 팩터리 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-578">Use the factory pattern instead.</span></span>
* <span data-ttu-id="02a46-579">루트 범위에서 임시 또는 범위가 지정된 <xref:System.IDisposable> 인스턴스를 확인하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-579">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="02a46-580">일반적으로 유일한 예외는 앱이 이상적 패턴이 아닌 <xref:System.IServiceProvider>를 생성/재생성 및 삭제하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-580">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="02a46-581">DI를 통한 <xref:System.IDisposable> 종속성 수신은 수신자가 자체적으로 <xref:System.IDisposable>를 구현할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-581">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="02a46-582"><xref:System.IDisposable> 종속성의 수신자는 해당 종속성에서 <xref:System.IDisposable.Dispose%2A>를 호출하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-582">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="02a46-583">범위는 서비스의 수명을 제어하는 데 사용되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-583">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="02a46-584">범위는 계층적이지 않으며 범위 간 특수 연결이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-584">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="02a46-585">기본 서비스 컨테이너 바꾸기</span><span class="sxs-lookup"><span data-stu-id="02a46-585">Default service container replacement</span></span>

<span data-ttu-id="02a46-586">기본 제공 서비스 컨테이너는 프레임워크 및 대부분의 소비자 앱의 요구를 충족하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-586">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="02a46-587">기본 제공 컨테이너가 지원하지 않는 특정 기능이 필요하지 않는 한, 다음과 같은 기본 제공 컨테이너를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-587">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="02a46-588">속성 삽입</span><span class="sxs-lookup"><span data-stu-id="02a46-588">Property injection</span></span>
* <span data-ttu-id="02a46-589">이름에 기반한 삽입</span><span class="sxs-lookup"><span data-stu-id="02a46-589">Injection based on name</span></span>
* <span data-ttu-id="02a46-590">자식 컨테이너</span><span class="sxs-lookup"><span data-stu-id="02a46-590">Child containers</span></span>
* <span data-ttu-id="02a46-591">사용자 지정 수명 관리</span><span class="sxs-lookup"><span data-stu-id="02a46-591">Custom lifetime management</span></span>
* <span data-ttu-id="02a46-592">초기화 지연에 대한 `Func<T>` 지원</span><span class="sxs-lookup"><span data-stu-id="02a46-592">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="02a46-593">규칙 기반 등록</span><span class="sxs-lookup"><span data-stu-id="02a46-593">Convention-based registration</span></span>

<span data-ttu-id="02a46-594">ASP.NET Core 앱에서 사용할 수 있는 타사 컨테이너는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-594">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="02a46-595">Autofac</span><span class="sxs-lookup"><span data-stu-id="02a46-595">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="02a46-596">DryIoc</span><span class="sxs-lookup"><span data-stu-id="02a46-596">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="02a46-597">유예</span><span class="sxs-lookup"><span data-stu-id="02a46-597">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="02a46-598">LightInject</span><span class="sxs-lookup"><span data-stu-id="02a46-598">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="02a46-599">Lamar</span><span class="sxs-lookup"><span data-stu-id="02a46-599">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="02a46-600">Stashbox</span><span class="sxs-lookup"><span data-stu-id="02a46-600">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="02a46-601">Unity</span><span class="sxs-lookup"><span data-stu-id="02a46-601">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="02a46-602">스레드로부터의 안전성</span><span class="sxs-lookup"><span data-stu-id="02a46-602">Thread safety</span></span>

<span data-ttu-id="02a46-603">스레드로부터 안전한 싱글톤 서비스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-603">Create thread-safe singleton services.</span></span> <span data-ttu-id="02a46-604">싱글톤 서비스가 Transient 서비스에 대한 종속성을 갖는 경우 Transient 서비스는 싱글톤에서 사용되는 방식에 따라 스레드로부터 안전성이 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-604">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="02a46-605">[AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)의 두 번째 인수와 같은 단일 서비스의 팩터리 메서드는 스레드로부터 안전하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-605">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="02a46-606">형식(`static`) 생성자와 같이 이 메서드는 단일 스레드에서 한 번만 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-606">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="02a46-607">권장 사항</span><span class="sxs-lookup"><span data-stu-id="02a46-607">Recommendations</span></span>

* <span data-ttu-id="02a46-608">`async/await` 및 `Task` 기반 서비스 해결은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-608">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="02a46-609">C#은 비동기 생성자를 지원하지 않으므로, 서비스를 동기식으로 해결한 후 비동기 메서드를 사용하는 패턴이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-609">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="02a46-610">데이터 및 구성을 서비스 컨테이너에 직접 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-610">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="02a46-611">예를 들어 사용자의 쇼핑 카트는 일반적으로 서비스 컨테이너에 추가하지 말아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-611">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="02a46-612">구성은 [옵션 패턴](xref:fundamentals/configuration/options)을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-612">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="02a46-613">마찬가지로 다른 일부 개체에 대한 액세스를 허용하기 위해서만 존재하는 “데이터 보유자” 개체를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="02a46-613">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="02a46-614">DI를 통해 실제 항목을 요청하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-614">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="02a46-615">서비스에 정적 액세스를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="02a46-615">Avoid static access to services.</span></span> <span data-ttu-id="02a46-616">예를 들어 다른 곳에 사용하기 위해 [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices)를 정적으로 입력하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-616">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="02a46-617">‘서비스 로케이터 패턴’은 [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 전략을 혼합하므로 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-617">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="02a46-618">DI를 대신 사용할 수 있는 경우에는 <xref:System.IServiceProvider.GetService*>를 호출하여 서비스 인스턴스를 가져오지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-618">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="02a46-619">**잘못된 예:**</span><span class="sxs-lookup"><span data-stu-id="02a46-619">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="02a46-620">**올바른 예** :</span><span class="sxs-lookup"><span data-stu-id="02a46-620">**Correct** :</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="02a46-621">런타임에 <xref:System.IServiceProvider.GetService*>를 종속성을 확인하는 팩토리를 삽입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-621">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="02a46-622">`HttpContext`(예: [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))에 정적으로 액세스하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="02a46-622">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="02a46-623">모든 권장 사항과 마찬가지로, 권장 사항을 무시해야 하는 상황이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-623">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="02a46-624">예외는 드물게 발생하며 대부분 프레임워크 자체 내에서 특별한 경우에만 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-624">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="02a46-625">DI는 정적/전역 개체 액세스 패턴의 ‘대안’입니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-625">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="02a46-626">고정 개체 액세스와 함께 사용할 경우 DI의 장점을 실현할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="02a46-626">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="02a46-627">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="02a46-627">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="02a46-628">IDisposables in ASP.NET Core를 삭제하는 네 가지 방법</span><span class="sxs-lookup"><span data-stu-id="02a46-628">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="02a46-629">종속성 주입으로 ASP.NET Core에 정리 코드 작성(MSDN)</span><span class="sxs-lookup"><span data-stu-id="02a46-629">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="02a46-630">명시적 종속성 원칙</span><span class="sxs-lookup"><span data-stu-id="02a46-630">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="02a46-631">Inversion of Control 컨테이너 및 종속성 주입 패턴(Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="02a46-631">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* <span data-ttu-id="02a46-632">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)(ASP.NET Core DI의 여러 인터페이스를 사용하여 서비스를 등록하는 방법)</span><span class="sxs-lookup"><span data-stu-id="02a46-632">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
