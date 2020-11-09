---
title: ASP.NET Core에서 앱 시작
author: rick-anderson
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/startup
ms.openlocfilehash: 747b13abb0ce3fed2d1dc018c6dbf82db1ae7130
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052242"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="17e59-103">ASP.NET Core에서 앱 시작</span><span class="sxs-lookup"><span data-stu-id="17e59-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="17e59-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) 및 [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="17e59-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="17e59-105">`Startup` 클래스는 서비스와 응용 프로그램의 요청 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-105">The `Startup` class configures services and the app's request pipeline.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="17e59-106">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="17e59-106">The Startup class</span></span>

<span data-ttu-id="17e59-107">ASP.NET Core 앱은 규칙에 따라 `Startup`으로 이름이 지정된 `Startup` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="17e59-108">`Startup` 클래스는:</span><span class="sxs-lookup"><span data-stu-id="17e59-108">The `Startup` class:</span></span>

* <span data-ttu-id="17e59-109">선택적으로 앱의 *서비스* 를 구성하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="17e59-110">서비스는 앱 기능을 제공하는 재사용 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="17e59-111">서비스는 `ConfigureServices`에서 *등록* 되며 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 앱 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="17e59-112">앱의 요청 처리 파이프라인을 만드는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="17e59-113">`ConfigureServices` 및 `Configure`는 앱 시작 시 ASP.NET Core 런타임에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="17e59-114">앞의 샘플은 [Razor Pages](xref:razor-pages/index)에 해당하며 MVC 버전도 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>


<span data-ttu-id="17e59-115">앱의 [호스트](xref:fundamentals/index#host)가 빌드될 때 `Startup` 클래스가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="17e59-116">`Startup` 클래스는 일반적으로 호스트 작성기에서 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 메서드를 호출하여 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-116">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="17e59-117">호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="17e59-118">앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="17e59-119">그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="17e59-120">[제네릭 호스트](xref:fundamentals/host/generic-host)(<xref:Microsoft.Extensions.Hosting.IHostBuilder>)를 사용할 경우 다음 서비스 유형만 `Startup` 생성자에 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="17e59-121">`Configure` 메서드가 호출될 때까지 대부분의 서비스를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-121">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="17e59-122">여러 Startup</span><span class="sxs-lookup"><span data-stu-id="17e59-122">Multiple Startup</span></span>

<span data-ttu-id="17e59-123">앱에서 다양한 환경(예: `StartupDevelopment`)에 대해 별도의 `Startup` 클래스를 정의하면 런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-123">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="17e59-124">이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-124">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="17e59-125">앱이 개발 환경에서 실행되고 `Startup` 클래스 및 `StartupDevelopment` 클래스 모두를 포함하는 경우 `StartupDevelopment` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-125">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="17e59-126">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments#environment-based-startup-class-and-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-126">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="17e59-127">호스트에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-127">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="17e59-128">시작하는 동안 오류를 처리하는 방법은 [시작 예외 처리](xref:fundamentals/error-handling#startup-exception-handling)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-128">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="17e59-129">ConfigureServices 메서드</span><span class="sxs-lookup"><span data-stu-id="17e59-129">The ConfigureServices method</span></span>

<span data-ttu-id="17e59-130"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-130">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="17e59-131">선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-131">Optional.</span></span>
* <span data-ttu-id="17e59-132">`Configure` 메서드 전에 호스트에 의해 호출되어 앱의 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-132">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="17e59-133">여기서 [구성 옵션](xref:fundamentals/configuration/index)이 규칙에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-133">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="17e59-134">호스트는 `Startup` 메서드가 호출되기 전에 일부 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-134">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="17e59-135">자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-135">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="17e59-136">실질적인 설정이 필요한 기능의 경우 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 `Add{Service}` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-136">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="17e59-137">예를 들어 **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores 및 **Add**RazorPages가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-137">For example, **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

<span data-ttu-id="17e59-138">서비스 컨테이너에 서비스를 추가하면 앱 내 및 `Configure` 메서드에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-138">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="17e59-139">서비스는 [종속성 주입](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-139">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="17e59-140">Configure 메서드</span><span class="sxs-lookup"><span data-stu-id="17e59-140">The Configure method</span></span>

<span data-ttu-id="17e59-141"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드는 앱이 HTTP 요청에 응답하는 방식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-141">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="17e59-142">요청 파이프라인은 [미들웨어](xref:fundamentals/middleware/index) 구성 요소를 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 인스턴스에 추가하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-142">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="17e59-143">`IApplicationBuilder`는 `Configure` 메서드에 사용할 수 있지만 서비스 컨테이너에 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-143">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="17e59-144">호스팅이 `IApplicationBuilder`를 만들고 `Configure`에 직접 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-144">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="17e59-145">[ASP.NET Core 템플릿](/dotnet/core/tools/dotnet-new)은 다음을 지원하는 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-145">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="17e59-146">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="17e59-146">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="17e59-147">예외 처리기</span><span class="sxs-lookup"><span data-stu-id="17e59-147">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="17e59-148">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="17e59-148">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="17e59-149">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="17e59-149">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="17e59-150">정적 파일</span><span class="sxs-lookup"><span data-stu-id="17e59-150">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="17e59-151">ASP.NET Core [MVC](xref:mvc/overview) 및 [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="17e59-151">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="17e59-152">앞의 샘플은 [Razor Pages](xref:razor-pages/index)에 해당하며 MVC 버전도 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-152">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

<span data-ttu-id="17e59-153">각 `Use` 확장 메서드는 요청 파이프라인에 하나 이상의 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-153">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="17e59-154">예를 들어 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>는 [정적 파일](xref:fundamentals/static-files)을 제공하도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-154">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="17e59-155">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출하거나 적절한 경우 체인을 단락(short-circuiting)하는 일을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-155">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="17e59-156">`IWebHostEnvironment`, `ILoggerFactory` 같은 추가 서비스 또는 `ConfigureServices`에 정의된 항목은 `Configure` 메서드 시그니처에서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-156">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="17e59-157">이 서비스를 사용할 수 있는 경우 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-157">These services are injected if they're available.</span></span>

<span data-ttu-id="17e59-158">`IApplicationBuilder`를 사용하는 방법 및 미들웨어 처리 순서에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-158">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="17e59-159">Startup을 사용하지 않고 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="17e59-159">Configure services without Startup</span></span>

<span data-ttu-id="17e59-160">`Startup` 클래스를 사용하지 않고 서비스 및 요청 처리 파이프라인을 구성하려면 호스트 작성기에서 `ConfigureServices` 및 `Configure` 편의성 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-160">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="17e59-161">`ConfigureServices`에 대한 여러 호출은 서로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-161">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="17e59-162">여러 `Configure` 메서드 호출이 있는 경우 마지막 `Configure` 호출이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-162">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="17e59-163">시작 필터를 이용한 Startup 확장</span><span class="sxs-lookup"><span data-stu-id="17e59-163">Extend Startup with startup filters</span></span>

<span data-ttu-id="17e59-164"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter>는 다음 용도로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-164">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="17e59-165">`Use{Middleware}`를 명시적으로 호출하지 않고 앱의 [Configure](#the-configure-method) 미들웨어 파이프라인의 시작 또는 끝 부분에서 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-165">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="17e59-166">`IStartupFilter`는 ASP.NET Core에서 앱 작성자가 명시적으로 기본 미들웨어를 등록할 필요 없이 파이프라인의 시작 부분에 기본값을 추가하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-166">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="17e59-167">`IStartupFilter`는 앱 작성자를 대신하여 다른 구성 요소 호출 `Use{Middleware}`를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-167">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="17e59-168">`Configure` 메서드의 파이프라인을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-168">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="17e59-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*)는 라이브러리에서 추가된 미들웨어 이전 또는 이후에 실행할 미들웨어를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="17e59-170">`IStartupFilter`는 `Action<IApplicationBuilder>`를 받고 반환하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-170">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="17e59-171"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 앱의 요청 파이프라인을 구성하는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-171">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="17e59-172">자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-172">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="17e59-173">각 `IStartupFilter`는 요청 파이프라인에서 하나 이상의 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-173">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="17e59-174">필터는 서비스 컨테이너에 추가된 순서대로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-174">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="17e59-175">필터는 다음 필터에 컨트롤을 전달하기 전이나 후에 미들웨어를 추가할 수 있으므로 앱 파이프라인의 시작 또는 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-175">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="17e59-176">다음 예제에서는 `IStartupFilter`를 사용하여 미들웨어를 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-176">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="17e59-177">`RequestSetOptionsMiddleware` 미들웨어는 쿼리 문자열 매개 변수에서 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-177">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="17e59-178">`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-178">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="17e59-179">`IStartupFilter`는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>의 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-179">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

<span data-ttu-id="17e59-180">`option`에 대한 쿼리 문자열 매개 변수가 제공되는 경우 미들웨어는 ASP.NET Core 미들웨어가 응답을 렌더링하기 전에 값 할당을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-180">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="17e59-181">미들웨어 실행 순서는 `IStartupFilter` 등록 순서로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-181">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="17e59-182">여러 `IStartupFilter` 구현은 동일한 개체와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-182">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="17e59-183">순서 지정이 중요한 경우 해당 미들웨어가 실행해야 하는 순서와 일치하도록 해당 `IStartupFilter` 서비스 등록의 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-183">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="17e59-184">라이브러리는 `IStartupFilter`로 등록된 다른 앱 미들웨어 전이나 후에 실행하는 하나 이상의 `IStartupFilter` 구현으로 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-184">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="17e59-185">라이브러리의 `IStartupFilter`에 의해 추가되는 미들웨어 전에 `IStartupFilter` 미들웨어를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-185">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="17e59-186">라이브러리가 서비스 컨테이너에 추가되기 전에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-186">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="17e59-187">나중에 호출하려면 라이브러리가 추가된 후에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-187">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="17e59-188">시작 시 외부 어셈블리로부터 구성 추가</span><span class="sxs-lookup"><span data-stu-id="17e59-188">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="17e59-189"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-189">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="17e59-190">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-190">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17e59-191">추가 자료</span><span class="sxs-lookup"><span data-stu-id="17e59-191">Additional resources</span></span>

* [<span data-ttu-id="17e59-192">호스트</span><span class="sxs-lookup"><span data-stu-id="17e59-192">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="17e59-193">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="17e59-193">The Startup class</span></span>

<span data-ttu-id="17e59-194">ASP.NET Core 앱은 규칙에 따라 `Startup`으로 이름이 지정된 `Startup` 클래스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-194">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="17e59-195">`Startup` 클래스는:</span><span class="sxs-lookup"><span data-stu-id="17e59-195">The `Startup` class:</span></span>

* <span data-ttu-id="17e59-196">선택적으로 앱의 *서비스* 를 구성하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-196">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="17e59-197">서비스는 앱 기능을 제공하는 재사용 가능한 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-197">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="17e59-198">서비스는 `ConfigureServices`에서 *등록* 되며 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 앱 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-198">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="17e59-199">앱의 요청 처리 파이프라인을 만드는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-199">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="17e59-200">`ConfigureServices` 및 `Configure`는 앱 시작 시 ASP.NET Core 런타임에 의해 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-200">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

<span data-ttu-id="17e59-201">앱의 [호스트](xref:fundamentals/index#host)가 빌드될 때 `Startup` 클래스가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-201">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="17e59-202">`Startup` 클래스는 일반적으로 호스트 작성기에서 [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 메서드를 호출하여 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-202">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="17e59-203">호스트는 `Startup` 클래스 생성자에 사용할 수 있는 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-203">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="17e59-204">앱은 `ConfigureServices`를 통해 추가 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-204">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="17e59-205">그러면 호스트 및 앱 서비스 모두를 `Configure` 및 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-205">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="17e59-206">`Startup` 클래스에 대한 [종속성 주입](xref:fundamentals/dependency-injection)의 일반적인 용도는 다음을 삽입하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-206">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="17e59-207">환경별로 서비스를 구성하는 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="17e59-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="17e59-208">구성을 읽는 <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="17e59-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="17e59-209">`Startup.ConfigureServices`에서 로거를 만드는 <xref:Microsoft.Extensions.Logging.ILoggerFactory>.</span><span class="sxs-lookup"><span data-stu-id="17e59-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="17e59-210">`Configure` 메서드가 호출될 때까지 대부분의 서비스를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-210">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="17e59-211">여러 Startup</span><span class="sxs-lookup"><span data-stu-id="17e59-211">Multiple Startup</span></span>

<span data-ttu-id="17e59-212">앱에서 다양한 환경(예: `StartupDevelopment`)에 대해 별도의 `Startup` 클래스를 정의하면 런타임에 적절한 `Startup` 클래스가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-212">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="17e59-213">이름 접미사가 현재 환경과 일치하는 클래스에 우선 순위가 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-213">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="17e59-214">앱이 개발 환경에서 실행되고 `Startup` 클래스 및 `StartupDevelopment` 클래스 모두를 포함하는 경우 `StartupDevelopment` 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-214">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="17e59-215">자세한 내용은 [여러 환경 사용](xref:fundamentals/environments#environment-based-startup-class-and-methods)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-215">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="17e59-216">호스트에 대한 자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-216">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="17e59-217">시작하는 동안 오류를 처리하는 방법은 [시작 예외 처리](xref:fundamentals/error-handling#startup-exception-handling)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-217">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="17e59-218">ConfigureServices 메서드</span><span class="sxs-lookup"><span data-stu-id="17e59-218">The ConfigureServices method</span></span>

<span data-ttu-id="17e59-219"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-219">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="17e59-220">선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-220">Optional.</span></span>
* <span data-ttu-id="17e59-221">`Configure` 메서드 전에 호스트에 의해 호출되어 앱의 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-221">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="17e59-222">여기서 [구성 옵션](xref:fundamentals/configuration/index)이 규칙에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-222">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="17e59-223">호스트는 `Startup` 메서드가 호출되기 전에 일부 서비스를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-223">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="17e59-224">자세한 내용은 [호스트](xref:fundamentals/index#host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-224">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="17e59-225">실질적인 설정이 필요한 기능의 경우 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 `Add{Service}` 확장 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-225">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="17e59-226">예를 들어 **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores 및 **Add**RazorPages가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-226">For example, **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

<span data-ttu-id="17e59-227">서비스 컨테이너에 서비스를 추가하면 앱 내 및 `Configure` 메서드에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-227">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="17e59-228">서비스는 [종속성 주입](xref:fundamentals/dependency-injection) 또는 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>를 통해 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-228">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

<span data-ttu-id="17e59-229">`SetCompatibilityVersion`에 대한 자세한 내용은 [SetCompatibilityVersion](xref:mvc/compatibility-version)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-229">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="17e59-230">Configure 메서드</span><span class="sxs-lookup"><span data-stu-id="17e59-230">The Configure method</span></span>

<span data-ttu-id="17e59-231"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 메서드는 앱이 HTTP 요청에 응답하는 방식을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-231">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="17e59-232">요청 파이프라인은 [미들웨어](xref:fundamentals/middleware/index) 구성 요소를 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 인스턴스에 추가하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-232">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="17e59-233">`IApplicationBuilder`는 `Configure` 메서드에 사용할 수 있지만 서비스 컨테이너에 등록되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-233">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="17e59-234">호스팅이 `IApplicationBuilder`를 만들고 `Configure`에 직접 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-234">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="17e59-235">[ASP.NET Core 템플릿](/dotnet/core/tools/dotnet-new)은 다음을 지원하는 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-235">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="17e59-236">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="17e59-236">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="17e59-237">예외 처리기</span><span class="sxs-lookup"><span data-stu-id="17e59-237">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="17e59-238">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="17e59-238">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="17e59-239">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="17e59-239">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="17e59-240">정적 파일</span><span class="sxs-lookup"><span data-stu-id="17e59-240">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="17e59-241">ASP.NET Core [MVC](xref:mvc/overview) 및 [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="17e59-241">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>
* [<span data-ttu-id="17e59-242">GDPR(일반 데이터 보호 규정)</span><span class="sxs-lookup"><span data-stu-id="17e59-242">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

<span data-ttu-id="17e59-243">각 `Use` 확장 메서드는 요청 파이프라인에 하나 이상의 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-243">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="17e59-244">예를 들어 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>는 [정적 파일](xref:fundamentals/static-files)을 제공하도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-244">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="17e59-245">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 다음 구성 요소를 호출하거나 적절한 경우 체인을 단락(short-circuiting)하는 일을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-245">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="17e59-246">`IHostingEnvironment` 및 `ILoggerFactory` 같은 추가 서비스 또는 `ConfigureServices`에 정의된 항목은 `Configure` 메서드 시그니처에서 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-246">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="17e59-247">이 서비스를 사용할 수 있는 경우 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-247">These services are injected if they're available.</span></span>

<span data-ttu-id="17e59-248">`IApplicationBuilder`를 사용하는 방법 및 미들웨어 처리 순서에 대한 자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-248">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="17e59-249">Startup을 사용하지 않고 서비스 구성</span><span class="sxs-lookup"><span data-stu-id="17e59-249">Configure services without Startup</span></span>

<span data-ttu-id="17e59-250">`Startup` 클래스를 사용하지 않고 서비스 및 요청 처리 파이프라인을 구성하려면 호스트 작성기에서 `ConfigureServices` 및 `Configure` 편의성 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-250">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="17e59-251">`ConfigureServices`에 대한 여러 호출은 서로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-251">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="17e59-252">여러 `Configure` 메서드 호출이 있는 경우 마지막 `Configure` 호출이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-252">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="17e59-253">시작 필터를 이용한 Startup 확장</span><span class="sxs-lookup"><span data-stu-id="17e59-253">Extend Startup with startup filters</span></span>

<span data-ttu-id="17e59-254"><xref:Microsoft.AspNetCore.Hosting.IStartupFilter>는 다음 용도로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-254">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="17e59-255">`Use{Middleware}`를 명시적으로 호출하지 않고 앱의 [Configure](#the-configure-method) 미들웨어 파이프라인의 시작 또는 끝 부분에서 미들웨어를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-255">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="17e59-256">`IStartupFilter`는 ASP.NET Core에서 앱 작성자가 명시적으로 기본 미들웨어를 등록할 필요 없이 파이프라인의 시작 부분에 기본값을 추가하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-256">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="17e59-257">`IStartupFilter`는 앱 작성자를 대신하여 다른 구성 요소 호출 `Use{Middleware}`를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-257">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="17e59-258">`Configure` 메서드의 파이프라인을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-258">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="17e59-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*)는 라이브러리에서 추가된 미들웨어 이전 또는 이후에 실행할 미들웨어를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="17e59-260">`IStartupFilter`는 `Action<IApplicationBuilder>`를 받고 반환하는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-260">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="17e59-261"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>는 앱의 요청 파이프라인을 구성하는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-261">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="17e59-262">자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-262">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="17e59-263">각 `IStartupFilter`는 요청 파이프라인에서 하나 이상의 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-263">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="17e59-264">필터는 서비스 컨테이너에 추가된 순서대로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-264">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="17e59-265">필터는 다음 필터에 컨트롤을 전달하기 전이나 후에 미들웨어를 추가할 수 있으므로 앱 파이프라인의 시작 또는 끝에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-265">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="17e59-266">다음 예제에서는 `IStartupFilter`를 사용하여 미들웨어를 등록하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-266">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="17e59-267">`RequestSetOptionsMiddleware` 미들웨어는 쿼리 문자열 매개 변수에서 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-267">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="17e59-268">`RequestSetOptionsMiddleware`는 `RequestSetOptionsStartupFilter` 클래스에서 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-268">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="17e59-269">`IStartupFilter`는 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>의 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-269">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

<span data-ttu-id="17e59-270">`option`에 대한 쿼리 문자열 매개 변수가 제공되는 경우 미들웨어는 ASP.NET Core 미들웨어가 응답을 렌더링하기 전에 값 할당을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-270">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="17e59-271">미들웨어 실행 순서는 `IStartupFilter` 등록 순서로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-271">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="17e59-272">여러 `IStartupFilter` 구현은 동일한 개체와 상호 작용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-272">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="17e59-273">순서 지정이 중요한 경우 해당 미들웨어가 실행해야 하는 순서와 일치하도록 해당 `IStartupFilter` 서비스 등록의 순서를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-273">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="17e59-274">라이브러리는 `IStartupFilter`로 등록된 다른 앱 미들웨어 전이나 후에 실행하는 하나 이상의 `IStartupFilter` 구현으로 미들웨어를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-274">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="17e59-275">라이브러리의 `IStartupFilter`에 의해 추가되는 미들웨어 전에 `IStartupFilter` 미들웨어를 호출하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-275">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="17e59-276">라이브러리가 서비스 컨테이너에 추가되기 전에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-276">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="17e59-277">나중에 호출하려면 라이브러리가 추가된 후에 서비스 등록의 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-277">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="17e59-278">시작 시 외부 어셈블리로부터 구성 추가</span><span class="sxs-lookup"><span data-stu-id="17e59-278">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="17e59-279"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="17e59-279">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="17e59-280">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="17e59-280">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17e59-281">추가 자료</span><span class="sxs-lookup"><span data-stu-id="17e59-281">Additional resources</span></span>

* [<span data-ttu-id="17e59-282">호스트</span><span class="sxs-lookup"><span data-stu-id="17e59-282">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end