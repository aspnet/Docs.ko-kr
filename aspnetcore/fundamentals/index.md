---
title: ASP.NET Core 기본 사항
author: rick-anderson
description: ASP.NET Core 앱을 구축하기 위한 기본적인 개념을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/index
ms.openlocfilehash: 25348f8486ec6ccb53ebf527ad4519638dd5f73e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059379"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="d98ac-103">ASP.NET Core 기본 사항</span><span class="sxs-lookup"><span data-stu-id="d98ac-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d98ac-104">이 문서에서는 ASP.NET Core 앱을 개발하는 방법을 이해하는 데 도움이 되는 주요 토픽의 개요를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="d98ac-105">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="d98ac-105">The Startup class</span></span>

<span data-ttu-id="d98ac-106">`Startup` 클래스에서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="d98ac-107">앱에서 요구하는 서비스가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="d98ac-108">앱의 요청 처리 파이프라인이 일련의 미들웨어 구성 요소로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="d98ac-109">샘플 `Startup` 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="d98ac-110">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="d98ac-111">종속성 주입(서비스)</span><span class="sxs-lookup"><span data-stu-id="d98ac-111">Dependency injection (services)</span></span>

<span data-ttu-id="d98ac-112">ASP.NET Core는 구성된 서비스를 앱을 통해 사용할 수 있도록 만드는 기본 제공 DI(종속성 주입) 프레임워크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="d98ac-113">예를 들어 로깅 구성 요소는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="d98ac-114">서비스를 구성(또는 *등록* )하는 코드는 `Startup.ConfigureServices` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-114">Code to configure (or *register* ) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d98ac-115">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="d98ac-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="d98ac-116">서비스는 일반적으로 DI로부터 생성자 주입을 사용하여 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="d98ac-117">생성자 주입에서는 클래스가 필요한 형식 또는 인터페이스의 생성자 매개 변수를 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="d98ac-118">DI 프레임워크가 런타임에 이 서비스의 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="d98ac-119">다음 예제에서는 생성자 주입을 사용하여 DI로부터 `:::no-loc(Razor):::PagesMovieContext`를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-119">The following example uses constructor injection to resolve a `:::no-loc(Razor):::PagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="d98ac-120">기본 제공 IoC(Inversion of Control) 컨테이너가 앱의 모든 요구 사항을 충족하지 않을 경우 대신 타사 IoC 컨테이너를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="d98ac-121">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="d98ac-122">미들웨어</span><span class="sxs-lookup"><span data-stu-id="d98ac-122">Middleware</span></span>

<span data-ttu-id="d98ac-123">요청 처리 파이프라인은 일련의 미들웨어 구성 요소로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="d98ac-124">각 구성 요소는 `HttpContext`에 대해 작업을 수행한 후 파이프라인에 있는 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="d98ac-125">규칙에 따라 미들웨어 구성 요소는 `Startup.Configure` 메서드에서 `Use...` 확장 메서드를 호출하여 파이프라인에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="d98ac-126">예를 들어 정적 파일을 렌더링하도록 설정하려면 `UseStaticFiles`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="d98ac-127">다음 예제에서는 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="d98ac-128">ASP.NET Core는 풍부한 일련의 기본 제공 미들웨어를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="d98ac-129">사용자 지정 미들웨어 구성 요소를 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="d98ac-130">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="d98ac-131">호스트</span><span class="sxs-lookup"><span data-stu-id="d98ac-131">Host</span></span>

<span data-ttu-id="d98ac-132">ASP.NET Core 앱은 시작 시 ‘호스트’를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-132">On startup, an ASP.NET Core app builds a *host* .</span></span> <span data-ttu-id="d98ac-133">호스트는 다음과 같은 앱의 리소스를 모두 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="d98ac-134">HTTP 서버 구현</span><span class="sxs-lookup"><span data-stu-id="d98ac-134">An HTTP server implementation</span></span>
* <span data-ttu-id="d98ac-135">미들웨어 구성 요소</span><span class="sxs-lookup"><span data-stu-id="d98ac-135">Middleware components</span></span>
* <span data-ttu-id="d98ac-136">로깅</span><span class="sxs-lookup"><span data-stu-id="d98ac-136">Logging</span></span>
* <span data-ttu-id="d98ac-137">DI(종속성 주입) 서비스</span><span class="sxs-lookup"><span data-stu-id="d98ac-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="d98ac-138">Configuration</span><span class="sxs-lookup"><span data-stu-id="d98ac-138">Configuration</span></span>

<span data-ttu-id="d98ac-139">호스트에는 다음 두 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-139">There are two different hosts:</span></span> 

* <span data-ttu-id="d98ac-140">.NET 일반 호스트</span><span class="sxs-lookup"><span data-stu-id="d98ac-140">.NET Generic Host</span></span>
* <span data-ttu-id="d98ac-141">ASP.NET Core 웹 호스트</span><span class="sxs-lookup"><span data-stu-id="d98ac-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="d98ac-142">.NET 일반 호스트를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="d98ac-143">ASP.NET Core 웹 호스트는 이전 버전과의 호환성을 위해서만 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="d98ac-144">다음 예제에서는 .NET 일반 호스트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="d98ac-145">`CreateDefaultBuilder` 및 `ConfigureWebHostDefaults` 메서드는 다음과 같은 기본 옵션으로 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="d98ac-146">[Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="d98ac-147">*:::no-loc(appsettings.json):::* , *appsettings.{Environment Name}.json* , 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-147">Load configuration from *:::no-loc(appsettings.json):::* , *appsettings.{Environment Name}.json* , environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="d98ac-148">콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="d98ac-149">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="d98ac-150">비 웹 시나리오</span><span class="sxs-lookup"><span data-stu-id="d98ac-150">Non-web scenarios</span></span>

<span data-ttu-id="d98ac-151">제네릭 호스트를 통해 다른 형식의 앱에서 로깅, DI(종속성 주입), 구성 및 앱 수명 관리와 같은 교차 프레임워크 확장을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="d98ac-152">자세한 내용은 <xref:fundamentals/host/generic-host> 및 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="d98ac-153">서버</span><span class="sxs-lookup"><span data-stu-id="d98ac-153">Servers</span></span>

<span data-ttu-id="d98ac-154">ASP.NET Core 앱은 HTTP 요청을 수신하기 위해 HTTP 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="d98ac-155">해당 서버는 `HttpContext`에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 대한 요청을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="d98ac-156">Windows</span><span class="sxs-lookup"><span data-stu-id="d98ac-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="d98ac-157">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="d98ac-158">*Kestrel* 은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="d98ac-159">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="d98ac-160">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="d98ac-161">‘IIS HTTP 서버’는 IIS를 사용하는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="d98ac-162">이 서버를 사용하면 ASP.NET Core 앱 및 IIS는 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="d98ac-163">*HTTP.sys* 는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="d98ac-164">macOS</span><span class="sxs-lookup"><span data-stu-id="d98ac-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="d98ac-165">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d98ac-166">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d98ac-167">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="d98ac-168">Linux</span><span class="sxs-lookup"><span data-stu-id="d98ac-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="d98ac-169">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d98ac-170">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d98ac-171">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="d98ac-172">자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="d98ac-173">Configuration</span><span class="sxs-lookup"><span data-stu-id="d98ac-173">Configuration</span></span>

<span data-ttu-id="d98ac-174">ASP.NET Core는 정렬된 일련의 구성 공급 기업에서 이름-값 쌍으로 설정을 가져오는 구성 프레임워크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="d98ac-175">*.json* 파일, *.xml* 파일, 환경 변수 및 명령줄 인수와 같은 다양한 소스에서 기본 제공 구성 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="d98ac-176">다른 소스를 지원하려면 사용자 지정 구성 공급자를 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="d98ac-177">[기본적으로](xref:fundamentals/configuration/index#default) ASP.NET Core 앱은 *:::no-loc(appsettings.json):::* , 환경 변수, 명령줄 등에서 읽도록 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *:::no-loc(appsettings.json):::* , environment variables, the command line, and more.</span></span> <span data-ttu-id="d98ac-178">앱의 구성이 로드되면 환경 변수의 값이 *:::no-loc(appsettings.json):::* 의 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-178">When the app's configuration is loaded, values from environment variables override values from *:::no-loc(appsettings.json):::* .</span></span>

<span data-ttu-id="d98ac-179">관련 구성 값을 읽는 기본 방법은 [옵션 패턴](xref:fundamentals/configuration/options)를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d98ac-180">자세한 내용은 [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat)(옵션 패턴을 사용하여 계층적 구성 데이터 바인딩)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="d98ac-181">ASP.NET Core는 암호와 같은 기밀 구성 데이터의 관리를 위해 [비밀 관리자](xref:security/app-secrets#secret-manager)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="d98ac-182">프로덕션 비밀의 경우 [Azure Key Vault](xref:security/key-vault-configuration)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="d98ac-183">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="d98ac-184">환경</span><span class="sxs-lookup"><span data-stu-id="d98ac-184">Environments</span></span>

<span data-ttu-id="d98ac-185">`Development`, `Staging` 및 `Production`과 같은 실행 환경은 ASP.NET Core의 일급 개념입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="d98ac-186">`ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하여 앱을 실행 중인 환경을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="d98ac-187">ASP.NET Core는 앱 시작 시 해당 환경 변수를 읽고 `IWebHostEnvironment` 구현에서 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="d98ac-188">이 구현은 DI(종속성 주입)를 통해 앱의 어디서나 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="d98ac-189">다음 예제에서는 앱이 `Development` 환경에서 실행될 때 상세한 오류 정보를 제공하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="d98ac-190">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="d98ac-191">로깅</span><span class="sxs-lookup"><span data-stu-id="d98ac-191">Logging</span></span>

<span data-ttu-id="d98ac-192">ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d98ac-193">사용 가능한 공급자는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-193">Available providers include:</span></span>

* <span data-ttu-id="d98ac-194">Console</span><span class="sxs-lookup"><span data-stu-id="d98ac-194">Console</span></span>
* <span data-ttu-id="d98ac-195">디버그</span><span class="sxs-lookup"><span data-stu-id="d98ac-195">Debug</span></span>
* <span data-ttu-id="d98ac-196">Windows 이벤트 추적</span><span class="sxs-lookup"><span data-stu-id="d98ac-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="d98ac-197">Windows 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="d98ac-197">Windows Event Log</span></span>
* <span data-ttu-id="d98ac-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d98ac-198">TraceSource</span></span>
* <span data-ttu-id="d98ac-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d98ac-199">Azure App Service</span></span>
* <span data-ttu-id="d98ac-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="d98ac-200">Azure Application Insights</span></span>

<span data-ttu-id="d98ac-201">로그를 만들려면 DI(종속성 주입)로부터 <xref:Microsoft.Extensions.Logging.ILogger%601> 서비스를 확인하고 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>과 같은 로깅 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="d98ac-202">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="d98ac-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="d98ac-203">`LogInformation`과 같은 로깅 메서드는 임의 개수의 필드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="d98ac-204">필드는 일반적으로 메시지 `string`을 생성하는 데 사용되지만 일부 로깅 공급자는 이들 필드를 별도의 필드로 데이터 저장소로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="d98ac-205">이 기능을 사용하면 로그 공급 기업이 [구조적 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d98ac-206">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="d98ac-207">라우팅</span><span class="sxs-lookup"><span data-stu-id="d98ac-207">Routing</span></span>

<span data-ttu-id="d98ac-208">*경로* 는 처리기에 매핑되는 URL 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="d98ac-209">처리기는 일반적으로 :::no-loc(Razor)::: Page, MVC 컨트롤러의 작업 메서드 또는 미들웨어와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-209">The handler is typically a :::no-loc(Razor)::: page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="d98ac-210">ASP.NET Core 라우팅을 사용하면 앱에서 사용되는 URL을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="d98ac-211">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="d98ac-212">오류 처리</span><span class="sxs-lookup"><span data-stu-id="d98ac-212">Error handling</span></span>

<span data-ttu-id="d98ac-213">ASP.NET Core에는 다음과 같은 오류를 처리하기 위한 기본 제공 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="d98ac-214">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="d98ac-214">A developer exception page</span></span>
* <span data-ttu-id="d98ac-215">사용자 지정 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="d98ac-215">Custom error pages</span></span>
* <span data-ttu-id="d98ac-216">정적 상태 코드 페이지</span><span class="sxs-lookup"><span data-stu-id="d98ac-216">Static status code pages</span></span>
* <span data-ttu-id="d98ac-217">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="d98ac-217">Startup exception handling</span></span>

<span data-ttu-id="d98ac-218">자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="d98ac-219">HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="d98ac-219">Make HTTP requests</span></span>

<span data-ttu-id="d98ac-220">`HttpClient` 인스턴스를 만들기 위해 `IHttpClientFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="d98ac-221">팩터리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-221">The factory:</span></span>

* <span data-ttu-id="d98ac-222">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d98ac-223">예를 들어, GitHub에 액세스하는 *github* 클라이언트를 등록 및 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="d98ac-224">다른 용도를 위한 기본 클라이언트를 등록 및 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="d98ac-225">나가는 요청 미들웨어 파이프라인을 빌드하기 위해 여러 위임 처리기를 연결하고 등록하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d98ac-226">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="d98ac-227">이 패턴은 캐싱, 오류 처리, serialization 및 로깅을 포함한 HTTP 요청 관련 횡단 문제를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="d98ac-228">일시적인 오류를 처리하기 위해 널리 사용되는 타사 라이브러리인 *Polly* 와 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-228">Integrates with *Polly* , a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="d98ac-229">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="d98ac-230">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 <xref:Microsoft.Extensions.Logging.ILogger>를 통해 구성 가능한 로깅 환경을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d98ac-231">자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="d98ac-232">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d98ac-232">Content root</span></span>

<span data-ttu-id="d98ac-233">콘텐츠 루트는 다음의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-233">The content root is the base path for:</span></span>

* <span data-ttu-id="d98ac-234">앱을 호스트하는 실행 파일( *.exe* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-234">The executable hosting the app ( *.exe* ).</span></span>
* <span data-ttu-id="d98ac-235">앱을 구성하는 컴파일된 어셈블리( *.dll* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-235">Compiled assemblies that make up the app ( *.dll* ).</span></span>
* <span data-ttu-id="d98ac-236">앱에서 사용하는 다음과 같은 콘텐츠 파일:</span><span class="sxs-lookup"><span data-stu-id="d98ac-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="d98ac-237">:::no-loc(Razor)::: 파일( *.cshtml* , *.razor* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-237">:::no-loc(Razor)::: files ( *.cshtml* , *.razor* )</span></span>
  * <span data-ttu-id="d98ac-238">구성 파일( *.json* , *.xml* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-238">Configuration files ( *.json* , *.xml* )</span></span>
  * <span data-ttu-id="d98ac-239">데이터 파일( *.db* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-239">Data files ( *.db* )</span></span>
* <span data-ttu-id="d98ac-240">[웹 루트](#web-root)(일반적으로 *wwwroot* 폴더)</span><span class="sxs-lookup"><span data-stu-id="d98ac-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="d98ac-241">개발 중에는 콘텐츠 루트가 기본적으로 프로젝트의 루트 디렉터리로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="d98ac-242">이 디렉터리는 앱의 콘텐츠 파일과 [웹 루트](#web-root)의 기본 경로이기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="d98ac-243">[호스트를 빌드](#host)할 때는 경로를 설정하여 다른 콘텐츠 루트를 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="d98ac-244">자세한 내용은 [콘텐츠 루트](xref:fundamentals/host/generic-host#contentroot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="d98ac-245">웹 루트</span><span class="sxs-lookup"><span data-stu-id="d98ac-245">Web root</span></span>

<span data-ttu-id="d98ac-246">웹 루트는 다음과 같은 퍼블릭 정적 리소스 파일의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="d98ac-247">스타일시트( *.css* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-247">Stylesheets ( *.css* )</span></span>
* <span data-ttu-id="d98ac-248">JavaScript( *.js* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-248">JavaScript ( *.js* )</span></span>
* <span data-ttu-id="d98ac-249">이미지( *.png* , *.jpg* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-249">Images ( *.png* , *.jpg* )</span></span>

<span data-ttu-id="d98ac-250">정적 파일은 기본적으로 웹 루트 디렉터리와 그 하위 디렉터리에서만 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="d98ac-251">웹 루트 경로는 기본적으로 *{content root}/wwwroot* 로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-251">The web root path defaults to *{content root}/wwwroot* .</span></span> <span data-ttu-id="d98ac-252">[호스트를 빌드](#host)할 때는 경로를 설정하여 다른 웹 루트를 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="d98ac-253">자세한 내용은 [웹 루트](xref:fundamentals/host/generic-host#webroot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="d98ac-254">프로젝트 파일에서 [\<Content> 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items#content)을 사용하여 *wwwroot* 에 파일을 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="d98ac-255">다음 예제에서는 *wwwroot/local* 과 그 하위 디렉터리에서 콘텐츠가 게시되지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="d98ac-256">:::no-loc(Razor)::: *.cshtml* 파일에서는 물결표 슬래시(`~/`)가 웹 루트를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-256">In :::no-loc(Razor)::: *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="d98ac-257">`~/`(으)로 시작하는 경로를 *가상 경로* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-257">A path beginning with `~/` is referred to as a *virtual path* .</span></span>

<span data-ttu-id="d98ac-258">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d98ac-259">이 문서는 ASP.NET Core 앱을 개발하는 방법을 이해하기 위한 주요 항목의 개요입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="d98ac-260">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="d98ac-260">The Startup class</span></span>

<span data-ttu-id="d98ac-261">`Startup` 클래스에서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="d98ac-262">앱에서 요구하는 서비스가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="d98ac-263">요청 처리 파이프라인이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="d98ac-264">*서비스* 는 앱에서 사용되는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="d98ac-265">예를 들어 로깅 구성 요소는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-265">For example, a logging component is a service.</span></span> <span data-ttu-id="d98ac-266">서비스를 구성(또는 *등록* )하는 코드는 `Startup.ConfigureServices` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-266">Code to configure (or *register* ) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="d98ac-267">요청 처리 파이프라인은 일련의 *미들웨어* 구성 요소로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="d98ac-268">예를 들어 미들웨어는 정적 파일에 대한 요청을 처리하거나 HTTP 요청을 HTTPS로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="d98ac-269">각 미들웨어는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="d98ac-270">요청 처리 파이프라인을 구성하는 코드는 `Startup.Configure` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="d98ac-271">샘플 `Startup` 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="d98ac-272">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="d98ac-273">종속성 주입(서비스)</span><span class="sxs-lookup"><span data-stu-id="d98ac-273">Dependency injection (services)</span></span>

<span data-ttu-id="d98ac-274">ASP.NET Core에는 구성된 서비스를 앱의 클래스에 사용할 수 있도록 만드는 기본 제공 DI(종속성 주입) 프레임워크가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="d98ac-275">클래스에서 서비스의 인스턴스를 가져오는 한 가지 방법은 필수 형식의 매개 변수를 사용하여 생성자를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="d98ac-276">해당 매개 변수는 서비스 형식 또는 인터페이스일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="d98ac-277">DI 시스템에서는 런타임 시 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="d98ac-278">Entity Framework Core 컨텍스트 개체를 가져오는 데 DI를 사용하는 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="d98ac-279">강조 표시된 줄은 생성자 주입의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="d98ac-280">DI가 기본 제공되기는 하지만 원하는 경우 타사 IoC(Inversion of Control) 컨테이너를 플러그 인할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="d98ac-281">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="d98ac-282">미들웨어</span><span class="sxs-lookup"><span data-stu-id="d98ac-282">Middleware</span></span>

<span data-ttu-id="d98ac-283">요청 처리 파이프라인은 일련의 미들웨어 구성 요소로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="d98ac-284">각 구성 요소는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="d98ac-285">규칙에 따라 미들웨어 구성 요소는 `Startup.Configure` 메서드에서 `Use...` 확장 메서드를 호출하여 파이프라인에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="d98ac-286">예를 들어 정적 파일을 렌더링하도록 설정하려면 `UseStaticFiles`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="d98ac-287">다음 예제에서 강조 표시된 코드는 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="d98ac-288">ASP.NET Core는 풍부한 일련의 기본 제공 미들웨어를 포함하고 있으며, 사용자 지정 미들웨어를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="d98ac-289">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="d98ac-290">호스트</span><span class="sxs-lookup"><span data-stu-id="d98ac-290">Host</span></span>

<span data-ttu-id="d98ac-291">ASP.NET Core 앱은 시작 시 *호스트* 를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="d98ac-292">호스트는 다음과 같은 앱의 리소스를 모두 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="d98ac-293">HTTP 서버 구현</span><span class="sxs-lookup"><span data-stu-id="d98ac-293">An HTTP server implementation</span></span>
* <span data-ttu-id="d98ac-294">미들웨어 구성 요소</span><span class="sxs-lookup"><span data-stu-id="d98ac-294">Middleware components</span></span>
* <span data-ttu-id="d98ac-295">로깅</span><span class="sxs-lookup"><span data-stu-id="d98ac-295">Logging</span></span>
* <span data-ttu-id="d98ac-296">DI</span><span class="sxs-lookup"><span data-stu-id="d98ac-296">DI</span></span>
* <span data-ttu-id="d98ac-297">Configuration</span><span class="sxs-lookup"><span data-stu-id="d98ac-297">Configuration</span></span>

<span data-ttu-id="d98ac-298">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="d98ac-299">두 호스트, 웹 호스트와 제네릭 호스트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="d98ac-300">ASP.NET Core 2.x에서 제네릭 호스트는 비 웹 시나리오에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="d98ac-301">호스트를 만드는 코드는 `Program.Main`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="d98ac-302">`CreateDefaultBuilder` 메서드는 다음과 같이 일반적으로 사용되는 옵션으로 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="d98ac-303">[Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="d98ac-304">*:::no-loc(appsettings.json):::* , *appsettings.{Environment Name}.json* , 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-304">Load configuration from *:::no-loc(appsettings.json):::* , *appsettings.{Environment Name}.json* , environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="d98ac-305">콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="d98ac-306">자세한 내용은 <xref:fundamentals/host/web-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="d98ac-307">비 웹 시나리오</span><span class="sxs-lookup"><span data-stu-id="d98ac-307">Non-web scenarios</span></span>

<span data-ttu-id="d98ac-308">제네릭 호스트를 통해 다른 형식의 앱에서 로깅, DI(종속성 주입), 구성 및 앱 수명 관리와 같은 교차 프레임워크 확장을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="d98ac-309">자세한 내용은 <xref:fundamentals/host/generic-host> 및 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="d98ac-310">서버</span><span class="sxs-lookup"><span data-stu-id="d98ac-310">Servers</span></span>

<span data-ttu-id="d98ac-311">ASP.NET Core 앱은 HTTP 요청을 수신하기 위해 HTTP 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="d98ac-312">해당 서버는 `HttpContext`에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 대한 요청을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="d98ac-313">Windows</span><span class="sxs-lookup"><span data-stu-id="d98ac-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="d98ac-314">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="d98ac-315">*Kestrel* 은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="d98ac-316">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="d98ac-317">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="d98ac-318">‘IIS HTTP 서버’는 IIS를 사용하는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="d98ac-319">이 서버를 사용하면 ASP.NET Core 앱 및 IIS는 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="d98ac-320">*HTTP.sys* 는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="d98ac-321">macOS</span><span class="sxs-lookup"><span data-stu-id="d98ac-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="d98ac-322">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d98ac-323">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d98ac-324">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="d98ac-325">Linux</span><span class="sxs-lookup"><span data-stu-id="d98ac-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="d98ac-326">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d98ac-327">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d98ac-328">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="d98ac-329">Windows</span><span class="sxs-lookup"><span data-stu-id="d98ac-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="d98ac-330">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="d98ac-331">*Kestrel* 은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="d98ac-332">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="d98ac-333">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="d98ac-334">*HTTP.sys* 는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="d98ac-335">macOS</span><span class="sxs-lookup"><span data-stu-id="d98ac-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="d98ac-336">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d98ac-337">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d98ac-338">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="d98ac-339">Linux</span><span class="sxs-lookup"><span data-stu-id="d98ac-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="d98ac-340">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="d98ac-341">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="d98ac-342">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d98ac-343">자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="d98ac-344">Configuration</span><span class="sxs-lookup"><span data-stu-id="d98ac-344">Configuration</span></span>

<span data-ttu-id="d98ac-345">ASP.NET Core는 정렬된 일련의 구성 공급 기업에서 이름-값 쌍으로 설정을 가져오는 구성 프레임워크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="d98ac-346">*.json* 파일, *.xml* 파일, 환경 변수 및 명령줄 인수와 같은 다양한 원본의 기본 제공 구성 공급 기업이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="d98ac-347">또는 사용자 지정 구성 공급 기업을 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="d98ac-348">예를 들어 구성이 *:::no-loc(appsettings.json):::* 및 환경 변수에서 제공되도록 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-348">For example, you could specify that configuration comes from *:::no-loc(appsettings.json):::* and environment variables.</span></span> <span data-ttu-id="d98ac-349">그런 다음, *ConnectionString* 값이 요청되면 프레임워크는 먼저 *:::no-loc(appsettings.json):::* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="d98ac-350">값을 찾았지만 환경 변수에서도 값을 찾을 수 있다면 환경 변수의 값이 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="d98ac-351">ASP.NET Core는 암호와 같은 기밀 구성 데이터를 관리하기 위해 [비밀 관리자 도구](xref:security/app-secrets)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d98ac-352">프로덕션 비밀의 경우 [Azure Key Vault](xref:security/key-vault-configuration)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="d98ac-353">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="d98ac-354">옵션</span><span class="sxs-lookup"><span data-stu-id="d98ac-354">Options</span></span>

<span data-ttu-id="d98ac-355">가능할 경우 ASP.NET Core는 구성 값을 저장하고 검색하기 위해 *옵션 패턴* 을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="d98ac-356">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="d98ac-357">예를 들어 다음 코드에서는 WebSockets 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="d98ac-358">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="d98ac-359">환경</span><span class="sxs-lookup"><span data-stu-id="d98ac-359">Environments</span></span>

<span data-ttu-id="d98ac-360">*개발* , *준비* 및 *프로덕션* 과 같은 실행 환경은 ASP.NET Core의 일급 개념입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-360">Execution environments, such as *Development* , *Staging* , and *Production* , are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="d98ac-361">`ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하여 앱을 실행 중인 환경을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="d98ac-362">ASP.NET Core는 앱 시작 시 해당 환경 변수를 읽고 `IHostingEnvironment` 구현에서 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="d98ac-363">환경 개체는 DI를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="d98ac-364">`Startup` 클래스의 다음 샘플 코드는 개발 중에 실행할 때만 자세한 오류 정보를 제공하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="d98ac-365">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="d98ac-366">로깅</span><span class="sxs-lookup"><span data-stu-id="d98ac-366">Logging</span></span>

<span data-ttu-id="d98ac-367">ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d98ac-368">지원되는 공급 기업에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-368">Available providers include the following:</span></span>

* <span data-ttu-id="d98ac-369">Console</span><span class="sxs-lookup"><span data-stu-id="d98ac-369">Console</span></span>
* <span data-ttu-id="d98ac-370">디버그</span><span class="sxs-lookup"><span data-stu-id="d98ac-370">Debug</span></span>
* <span data-ttu-id="d98ac-371">Windows 이벤트 추적</span><span class="sxs-lookup"><span data-stu-id="d98ac-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="d98ac-372">Windows 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="d98ac-372">Windows Event Log</span></span>
* <span data-ttu-id="d98ac-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d98ac-373">TraceSource</span></span>
* <span data-ttu-id="d98ac-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d98ac-374">Azure App Service</span></span>
* <span data-ttu-id="d98ac-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="d98ac-375">Azure Application Insights</span></span>

<span data-ttu-id="d98ac-376">DI에서 `ILogger` 개체를 가져오고 로그 메서드를 호출하여 앱 코드 어디서나 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="d98ac-377">생성자 주입 및 로깅 메서드 호출을 강조 표시하고 `ILogger` 개체를 사용하는 샘플 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="d98ac-378">`ILogger` 인터페이스를 통해 필드 개수에 관계 없이 로그 공급 기업에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="d98ac-379">필드는 일반적으로 메시지 문자열을 생성하는 데 사용되지만 공급 기업은 이를 별도 필드로 데이터 저장소에 보낼 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="d98ac-380">이 기능을 사용하면 로그 공급 기업이 [구조적 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d98ac-381">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="d98ac-382">라우팅</span><span class="sxs-lookup"><span data-stu-id="d98ac-382">Routing</span></span>

<span data-ttu-id="d98ac-383">*경로* 는 처리기에 매핑되는 URL 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="d98ac-384">처리기는 일반적으로 :::no-loc(Razor)::: Page, MVC 컨트롤러의 작업 메서드 또는 미들웨어와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-384">The handler is typically a :::no-loc(Razor)::: page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="d98ac-385">ASP.NET Core 라우팅을 사용하면 앱에서 사용되는 URL을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="d98ac-386">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="d98ac-387">오류 처리</span><span class="sxs-lookup"><span data-stu-id="d98ac-387">Error handling</span></span>

<span data-ttu-id="d98ac-388">ASP.NET Core에는 다음과 같은 오류를 처리하기 위한 기본 제공 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="d98ac-389">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="d98ac-389">A developer exception page</span></span>
* <span data-ttu-id="d98ac-390">사용자 지정 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="d98ac-390">Custom error pages</span></span>
* <span data-ttu-id="d98ac-391">정적 상태 코드 페이지</span><span class="sxs-lookup"><span data-stu-id="d98ac-391">Static status code pages</span></span>
* <span data-ttu-id="d98ac-392">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="d98ac-392">Startup exception handling</span></span>

<span data-ttu-id="d98ac-393">자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="d98ac-394">HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="d98ac-394">Make HTTP requests</span></span>

<span data-ttu-id="d98ac-395">`HttpClient` 인스턴스를 만들기 위해 `IHttpClientFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="d98ac-396">팩터리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-396">The factory:</span></span>

* <span data-ttu-id="d98ac-397">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d98ac-398">예를 들어, *github* 클라이언트는 GitHub에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="d98ac-399">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="d98ac-400">나가는 요청 미들웨어 파이프라인을 빌드하기 위해 여러 위임 처리기를 연결하고 등록하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d98ac-401">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="d98ac-402">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="d98ac-403">일시적인 오류를 처리하기 위해 널리 사용되는 타사 라이브러리인 *Polly* 와 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-403">Integrates with *Polly* , a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="d98ac-404">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d98ac-405">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d98ac-406">자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="d98ac-407">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="d98ac-407">Content root</span></span>

<span data-ttu-id="d98ac-408">콘텐츠 루트는 다음의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="d98ac-409">앱을 호스트하는 실행 파일( *.exe* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-409">Executable hosting the app ( *.exe* ).</span></span>
* <span data-ttu-id="d98ac-410">앱을 구성하는 컴파일된 어셈블리( *.dll* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-410">Compiled assemblies that make up the app ( *.dll* ).</span></span>
* <span data-ttu-id="d98ac-411">다음과 같은 앱에서 사용하는 비코드 콘텐츠 파일:</span><span class="sxs-lookup"><span data-stu-id="d98ac-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="d98ac-412">:::no-loc(Razor)::: 파일( *.cshtml* , *.razor* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-412">:::no-loc(Razor)::: files ( *.cshtml* , *.razor* )</span></span>
  * <span data-ttu-id="d98ac-413">구성 파일( *.json* , *.xml* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-413">Configuration files ( *.json* , *.xml* )</span></span>
  * <span data-ttu-id="d98ac-414">데이터 파일( *.db* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-414">Data files ( *.db* )</span></span>
* <span data-ttu-id="d98ac-415">[웹 루트](#web-root)(일반적으로 *wwwroot* 폴더에 게시됨)</span><span class="sxs-lookup"><span data-stu-id="d98ac-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="d98ac-416">개발 중:</span><span class="sxs-lookup"><span data-stu-id="d98ac-416">During development:</span></span>

* <span data-ttu-id="d98ac-417">콘텐츠 루트의 기본값은 프로젝트의 루트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="d98ac-418">프로젝트의 루트 디렉터리는 다음을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="d98ac-419">프로젝트의 루트 디렉터리에서 앱의 비코드 콘텐츠 파일의 경로</span><span class="sxs-lookup"><span data-stu-id="d98ac-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="d98ac-420">[웹 루트](#web-root)(일반적으로 프로젝트의 루트 디렉터리의 *wwwroot* 폴더)</span><span class="sxs-lookup"><span data-stu-id="d98ac-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="d98ac-421">[호스트를 빌드](#host)할 때 지정될 수 있는 대체 콘텐츠 루트 경로</span><span class="sxs-lookup"><span data-stu-id="d98ac-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="d98ac-422">자세한 내용은 <xref:fundamentals/host/web-host#content-root>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="d98ac-423">웹 루트</span><span class="sxs-lookup"><span data-stu-id="d98ac-423">Web root</span></span>

<span data-ttu-id="d98ac-424">웹 루트는 다음과 같은 공용, 비코드, 정적 리소스 파일의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="d98ac-425">스타일시트( *.css* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-425">Stylesheets ( *.css* )</span></span>
* <span data-ttu-id="d98ac-426">JavaScript( *.js* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-426">JavaScript ( *.js* )</span></span>
* <span data-ttu-id="d98ac-427">이미지( *.png* , *.jpg* )</span><span class="sxs-lookup"><span data-stu-id="d98ac-427">Images ( *.png* , *.jpg* )</span></span>

<span data-ttu-id="d98ac-428">정적 파일은 기본적으로 웹 루트 디렉터리 및 하위 디렉터리에서만 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="d98ac-429">웹 루트 경로는 *{content root}/wwwroot* 를 기본값으로 지정하지만 [호스트를 빌드](#host)할 때 다른 웹 루트를 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-429">The web root path defaults to *{content root}/wwwroot* , but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="d98ac-430">자세한 내용은 [웹 루트](xref:fundamentals/host/web-host#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="d98ac-431">프로젝트 파일에서 [\<Content> 프로젝트 항목](/visualstudio/msbuild/common-msbuild-project-items#content)을 사용하여 *wwwroot* 에 파일을 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="d98ac-432">다음 예에서는 *wwwroot/local* 디렉터리 및 하위 디렉터리에 콘텐츠를 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="d98ac-433">:::no-loc(Razor):::( *.cshtml* ) 파일에서는 물결표 슬래시(`~/`)가 웹 루트를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-433">In :::no-loc(Razor)::: ( *.cshtml* ) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="d98ac-434">`~/`(으)로 시작하는 경로를 *가상 경로* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="d98ac-434">A path beginning with `~/` is referred to as a *virtual path* .</span></span>

<span data-ttu-id="d98ac-435">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d98ac-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
