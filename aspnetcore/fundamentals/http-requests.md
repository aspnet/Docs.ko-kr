---
title: ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기
author: stevejgordon
description: IHttpClientFactory 인터페이스를 사용하여 ASP.NET Core에서 논리적 HttpClient 인스턴스를 관리하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 1/21/2021
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
uid: fundamentals/http-requests
ms.openlocfilehash: 1cf3029452f87a396847f969f0f3136a75874752
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057332"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="d0187-103">ASP.NET Core에서 IHttpClientFactory를 사용하여 HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="d0187-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d0187-104">작성자: [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc) 및 [Ryan Nowak](https://github.com/rynowak).</span><span class="sxs-lookup"><span data-stu-id="d0187-104">By [Kirk Larkin](https://github.com/serpent5), [Steve Gordon](https://github.com/stevejgordon), [Glenn Condron](https://github.com/glennc), and [Ryan Nowak](https://github.com/rynowak).</span></span>

<span data-ttu-id="d0187-105">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="d0187-106">`IHttpClientFactory`는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="d0187-107">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d0187-108">예를 들어, *github* 라는 클라이언트를 [GitHub](https://github.com/)에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="d0187-109">일반적인 액세스를 위한 기본 클라이언트를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="d0187-110">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="d0187-111">Polly 기반 미들웨어에 대한 확장을 제공하여 `HttpClient`에서의 핸들러 위임을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="d0187-112">기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="d0187-113">자동 관리가 `HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS(Domain Name System) 문제를 방지해 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d0187-114">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d0187-115">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample)). 다운로드 예제는 영역을 테스트하기 위한 기초적인 앱을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d0187-116">이 항목 버전의 샘플 코드는 <xref:System.Text.Json>을 사용하여 HTTP 응답으로 반환된 JSON 콘텐츠를 역직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="d0187-117">`Json.NET` 및 `ReadAsAsync<T>`를 사용하는 샘플의 경우, 버전 선택기를 사용하여 이 항목의 2.x 버전을 선택하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="d0187-118">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="d0187-118">Consumption patterns</span></span>

<span data-ttu-id="d0187-119">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="d0187-120">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="d0187-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="d0187-121">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="d0187-122">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="d0187-123">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="d0187-124">가장 좋은 방법은 앱의 요구 사항에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="d0187-125">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="d0187-125">Basic usage</span></span>

<span data-ttu-id="d0187-126">`AddHttpClient`를 호출하여 `IHttpClientFactory`를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="d0187-127">[종속성 주입(DI)](xref:fundamentals/dependency-injection)을 사용하여 `IHttpClientFactory`를 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d0187-128">다음 코드는 `IHttpClientFactory`를 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="d0187-129">앞서 나온 예제에서와 같이 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링하는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="d0187-130">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="d0187-131">기존 앱에서 `HttpClient` 인스턴스가 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="d0187-132">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-132">Named clients</span></span>

<span data-ttu-id="d0187-133">명명된 클라이언트는 다음과 같은 경우에 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="d0187-134">앱에서 `HttpClient`를 서로 다른 곳에서 여러 번 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="d0187-135">여러 `HttpClient`가 서로 다른 구성을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="d0187-136">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="d0187-137">위의 코드에서 클라이언트는 다음을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="d0187-138">기본 주소 `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="d0187-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="d0187-139">GitHub API를 사용하는 데 필요한 헤더 2개.</span><span class="sxs-lookup"><span data-stu-id="d0187-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="d0187-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="d0187-140">CreateClient</span></span>

<span data-ttu-id="d0187-141"><xref:System.Net.Http.IHttpClientFactory.CreateClient*>가 호출될 때마다</span><span class="sxs-lookup"><span data-stu-id="d0187-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="d0187-142">`HttpClient`의 새 인스턴스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="d0187-143">구성 작업이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-143">The configuration action is called.</span></span>

<span data-ttu-id="d0187-144">명명된 클라이언트를 만들려면 `CreateClient`로 이름을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="d0187-145">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="d0187-146">클라이언트에 대해 구성된 기본 주소가 사용되었므로 코드는 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="d0187-147">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-147">Typed clients</span></span>

<span data-ttu-id="d0187-148">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="d0187-148">Typed clients:</span></span>

* <span data-ttu-id="d0187-149">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="d0187-150">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="d0187-151">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="d0187-152">예를 들어, 다음과 같은 경우에 단일 형식화된 클라이언트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="d0187-153">단일 백 엔드 엔드포인트에 대해.</span><span class="sxs-lookup"><span data-stu-id="d0187-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="d0187-154">엔드포인트를 처리하는 모든 로직을 캡슐화하기 위해.</span><span class="sxs-lookup"><span data-stu-id="d0187-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="d0187-155">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="d0187-156">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="d0187-157">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="d0187-157">In the preceding code:</span></span>

* <span data-ttu-id="d0187-158">구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="d0187-159">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="d0187-160">`HttpClient` 기능을 노출하는 API 특정 메서드를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="d0187-161">예를 들어, `GetAspNetDocsIssues` 메서드는 열린 문제를 검색하는 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="d0187-162">다음 코드는 `Startup.ConfigureServices`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*>를 호출하여 형식화된 클라이언트 클래스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="d0187-163">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="d0187-164">위의 코드에서 `AddHttpClient`는 `GitHubService`를 임시 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="d0187-165">이 등록에서는 팩터리 메서드를 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="d0187-166">`HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="d0187-167">`HttpClient`의 인스턴스를 생성자에 전달하여 `GitHubService`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="d0187-168">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="d0187-169">형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="d0187-170">형식화된 클라이언트 내에서 `HttpClient`를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="d0187-171">속성으로 노출하는 대신 내부적으로 `HttpClient` 인스턴스를 호출하는 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="d0187-172">위의 코드에서는 `HttpClient`가 private 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="d0187-173">`HttpClient`에 대한 액세스는 public `GetRepos` 메서드에 의해 이루어집니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="d0187-174">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-174">Generated clients</span></span>

<span data-ttu-id="d0187-175">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="d0187-176">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="d0187-177">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="d0187-178">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="d0187-179">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="d0187-180">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="d0187-181">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="make-post-put-and-delete-requests"></a><span data-ttu-id="d0187-182">POST, PUT 및 DELETE 요청 수행</span><span class="sxs-lookup"><span data-stu-id="d0187-182">Make POST, PUT, and DELETE requests</span></span>

<span data-ttu-id="d0187-183">위의 예제에서 모든 HTTP 요청은 GET HTTP 동사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-183">In the preceding examples, all HTTP requests use the GET HTTP verb.</span></span> <span data-ttu-id="d0187-184">`HttpClient`는 다음을 비롯한 다른 HTTP 동사도 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-184">`HttpClient` also supports other HTTP verbs, including:</span></span>

* <span data-ttu-id="d0187-185">POST</span><span class="sxs-lookup"><span data-stu-id="d0187-185">POST</span></span>
* <span data-ttu-id="d0187-186">PUT</span><span class="sxs-lookup"><span data-stu-id="d0187-186">PUT</span></span>
* <span data-ttu-id="d0187-187">Delete</span><span class="sxs-lookup"><span data-stu-id="d0187-187">DELETE</span></span>
* <span data-ttu-id="d0187-188">패치</span><span class="sxs-lookup"><span data-stu-id="d0187-188">PATCH</span></span>

<span data-ttu-id="d0187-189">지원되는 HTTP 동사의 전체 목록은 <xref:System.Net.Http.HttpMethod>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-189">For a complete list of supported HTTP verbs, see <xref:System.Net.Http.HttpMethod>.</span></span>

<span data-ttu-id="d0187-190">다음 예제에서는 HTTP POST 요청을 수행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-190">The following example shows how to make an HTTP POST request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_POST)]

<span data-ttu-id="d0187-191">위의 코드에서 `CreateItemAsync` 메서드는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-191">In the preceding code, the `CreateItemAsync` method:</span></span>

* <span data-ttu-id="d0187-192">`System.Text.Json`을 사용하여 `TodoItem` 매개 변수를 JSON으로 serialize합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-192">Serializes the `TodoItem` parameter to JSON using `System.Text.Json`.</span></span> <span data-ttu-id="d0187-193"><xref:System.Text.Json.JsonSerializerOptions>의 인스턴스를 사용하여 serialization 프로세스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-193">This uses an instance of <xref:System.Text.Json.JsonSerializerOptions> to configure the serialization process.</span></span>
* <span data-ttu-id="d0187-194">HTTP 요청의 본문에서 전송하기 위해 serialize된 JSON을 패키지할 <xref:System.Net.Http.StringContent>의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-194">Creates an instance of <xref:System.Net.Http.StringContent> to package the serialized JSON for sending in the HTTP request's body.</span></span>
* <span data-ttu-id="d0187-195"><xref:System.Net.Http.HttpClient.PostAsync%2A>를 호출하여 JSON 콘텐츠를 지정된 URL로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-195">Calls <xref:System.Net.Http.HttpClient.PostAsync%2A> to send the JSON content to the specified URL.</span></span> <span data-ttu-id="d0187-196">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress)에 추가되는 상대 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-196">This is a relative URL that gets added to the [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress).</span></span>
* <span data-ttu-id="d0187-197">응답 상태 코드가 성공을 나타내지 않을 경우 <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A>를 호출하여 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-197">Calls <xref:System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode%2A> to throw an exception if the response status code does not indicate success.</span></span>

<span data-ttu-id="d0187-198">`HttpClient`는 다른 형식의 콘텐츠도 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-198">`HttpClient` also supports other types of content.</span></span> <span data-ttu-id="d0187-199">예를 들어 <xref:System.Net.Http.MultipartContent> 및 <xref:System.Net.Http.StreamContent>를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-199">For example, <xref:System.Net.Http.MultipartContent> and <xref:System.Net.Http.StreamContent>.</span></span> <span data-ttu-id="d0187-200">지원되는 콘텐츠의 전체 목록은 <xref:System.Net.Http.HttpContent>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-200">For a complete list of supported content, see <xref:System.Net.Http.HttpContent>.</span></span>

<span data-ttu-id="d0187-201">다음 예제에서는 HTTP PUT 요청을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-201">The following example shows an HTTP PUT request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_PUT)]

<span data-ttu-id="d0187-202">앞의 코드는 POST 예제와 매우 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-202">The preceding code is very similar to the POST example.</span></span> <span data-ttu-id="d0187-203">`SaveItemAsync` 메서드는 `PostAsync` 대신 <xref:System.Net.Http.HttpClient.PutAsync%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-203">The `SaveItemAsync` method calls <xref:System.Net.Http.HttpClient.PutAsync%2A> instead of `PostAsync`.</span></span>

<span data-ttu-id="d0187-204">다음 예제에서는 HTTP DELETE 요청을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-204">The following example shows an HTTP DELETE request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/TodoClient.cs?name=snippet_DELETE)]

<span data-ttu-id="d0187-205">위의 코드에서 `DeleteItemAsync` 메서드는 <xref:System.Net.Http.HttpClient.DeleteAsync%2A>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-205">In the preceding code, the `DeleteItemAsync` method calls <xref:System.Net.Http.HttpClient.DeleteAsync%2A>.</span></span> <span data-ttu-id="d0187-206">HTTP DELETE 요청은 일반적으로 본문을 포함하지 않기 때문에 `DeleteAsync` 메서드는 `HttpContent`의 인스턴스를 허용하는 오버로드를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-206">Because HTTP DELETE requests typically contain no body, the `DeleteAsync` method doesn't provide an overload that accepts an instance of `HttpContent`.</span></span>

<span data-ttu-id="d0187-207">`HttpClient`에 다른 HTTP 동사를 사용하는 방법에 대한 자세한 내용은 <xref:System.Net.Http.HttpClient>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-207">To learn more about using different HTTP verbs with `HttpClient`, see <xref:System.Net.Http.HttpClient>.</span></span>

## <a name="outgoing-request-middleware"></a><span data-ttu-id="d0187-208">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d0187-208">Outgoing request middleware</span></span>

<span data-ttu-id="d0187-209">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-209">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="d0187-210">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="d0187-210">`IHttpClientFactory`:</span></span>

  * <span data-ttu-id="d0187-211">각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-211">Simplifies defining the handlers to apply for each named client.</span></span>
  * <span data-ttu-id="d0187-212">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-212">Supports registration and chaining of multiple handlers to build an outgoing request   middleware pipeline.</span></span> <span data-ttu-id="d0187-213">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-213">Each of these handlers is able to perform work before and after the   outgoing request.</span></span> <span data-ttu-id="d0187-214">이 패턴은</span><span class="sxs-lookup"><span data-stu-id="d0187-214">This pattern:</span></span>
  
    * <span data-ttu-id="d0187-215">ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-215">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
    * <span data-ttu-id="d0187-216">다음과 같은 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-216">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>
      * <span data-ttu-id="d0187-217">캐싱</span><span class="sxs-lookup"><span data-stu-id="d0187-217">caching</span></span>
      * <span data-ttu-id="d0187-218">오류 처리</span><span class="sxs-lookup"><span data-stu-id="d0187-218">error handling</span></span>
      * <span data-ttu-id="d0187-219">직렬화</span><span class="sxs-lookup"><span data-stu-id="d0187-219">serialization</span></span>
      * <span data-ttu-id="d0187-220">로깅</span><span class="sxs-lookup"><span data-stu-id="d0187-220">logging</span></span>

<span data-ttu-id="d0187-221">위임 처리기를 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-221">To create a delegating handler:</span></span>

* <span data-ttu-id="d0187-222"><xref:System.Net.Http.DelegatingHandler>를 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-222">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="d0187-223"><xref:System.Net.Http.DelegatingHandler.SendAsync*>을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-223">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="d0187-224">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-224">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="d0187-225">위의 코드는 `X-API-KEY` 헤더가 요청에 있는지 여부를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-225">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="d0187-226">`X-API-KEY`가 누락된 경우 <xref:System.Net.HttpStatusCode.BadRequest>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-226">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="d0187-227"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>가 있는 `HttpClient`의 구성에는 둘 이상의 핸들러가 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-227">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d0187-228">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-228">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="d0187-229">일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-229">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="d0187-230">여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-230">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="d0187-231">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-231">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

### <a name="use-di-in-outgoing-request-middleware"></a><span data-ttu-id="d0187-232">나가는 요청 미들웨어에 DI 사용</span><span class="sxs-lookup"><span data-stu-id="d0187-232">Use DI in outgoing request middleware</span></span>

<span data-ttu-id="d0187-233">`IHttpClientFactory`는 새 위임 처리기를 만들 때 처리기의 생성자 매개 변수를 충족시키기 위해 DI를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-233">When `IHttpClientFactory` creates a new delegating handler, it uses DI to fulfill the handler's constructor parameters.</span></span> <span data-ttu-id="d0187-234">`IHttpClientFactory`는 각 처리기에 대해 **별도의** DI 범위를 만들기 때문에 처리기가 범위가 지정된 서비스를 사용할 때 놀라운 동작이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-234">`IHttpClientFactory` creates a **separate** DI scope for each handler, which can lead to surprising behavior when a handler consumes a *scoped* service.</span></span>

<span data-ttu-id="d0187-235">예를 들어 다음의 식별자가 포함된 연산자로 작업을 나타내는 다음의 인터페이스 및 구현을 고려합니다. `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="d0187-235">For example, consider the following interface and its implementation, which represents a task as an operation with an identifier, `OperationId`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Models/OperationScoped.cs?name=snippet_Types)]

<span data-ttu-id="d0187-236">이름에서 알 수 있듯 `IOperationScoped`는 범위가 지정된 수명을 사용하여 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-236">As its name suggests, `IOperationScoped` is registered with DI using a *scoped* lifetime:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Startup.cs?name=snippet_IOperationScoped&highlight=18,26)]

<span data-ttu-id="d0187-237">다음 위임 처리기는 `IOperationScoped`를 사용하여 `X-OPERATION-ID` 나가는 요청의 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-237">The following delegating handler consumes and uses `IOperationScoped` to set the `X-OPERATION-ID` header for the outgoing request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpRequestsSample/Handlers/OperationHandler.cs?name=snippet_Class&highlight=13)]

<span data-ttu-id="d0187-238">[`HttpRequestsSample` 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)]에서 `/Operation`으로 이동한 뒤 페이지를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-238">In the [`HttpRequestsSample` download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples/3.x/HttpRequestsSample)], navigate to `/Operation` and refresh the page.</span></span> <span data-ttu-id="d0187-239">요청 범위 값은 각 요청마다 변경되지만 처리기 범위 값은 5초마다 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-239">The request scope value changes for each request, but the handler scope value only changes every 5 seconds.</span></span>

<span data-ttu-id="d0187-240">처리기는 모든 범위의 서비스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-240">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="d0187-241">처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-241">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="d0187-242">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-242">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="d0187-243">[HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties)를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-243">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="d0187-244"><xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-244">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="d0187-245">사용자 지정 <xref:System.Threading.AsyncLocal`1> 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-245">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="d0187-246">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="d0187-246">Use Polly-based handlers</span></span>

<span data-ttu-id="d0187-247">`IHttpClientFactory`는 타사 라이브러리 [Polly](https://github.com/App-vNext/Polly)와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-247">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="d0187-248">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-248">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="d0187-249">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-249">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="d0187-250">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-250">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="d0187-251">Polly 확장은 클라이언트에 Polly 기반 처리기를 추가하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-251">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="d0187-252">Polly를 사용하려면 [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-252">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="d0187-253">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="d0187-253">Handle transient faults</span></span>

<span data-ttu-id="d0187-254">오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-254">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="d0187-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용하면 정책에서 일시적인 오류를 처리하도록 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-255"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="d0187-256">`AddTransientHttpErrorPolicy`를 사용하여 구성한 정책은 다음과 같은 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-256">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="d0187-257">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="d0187-257">HTTP 5xx</span></span>
* <span data-ttu-id="d0187-258">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="d0187-258">HTTP 408</span></span>

<span data-ttu-id="d0187-259">`AddTransientHttpErrorPolicy`는 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-259">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="d0187-260">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-260">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="d0187-261">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-261">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="d0187-262">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="d0187-262">Dynamically select policies</span></span>

<span data-ttu-id="d0187-263">Polly 기반 처리기를 추가하는 데 사용할 수 있는 확장 메서드(예: <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>)가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-263">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="d0187-264">다음 `AddPolicyHandler` 오버로드는 요청을 검사하여 어느 정책을 적용할지 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-264">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="d0187-265">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-265">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="d0187-266">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-266">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="d0187-267">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="d0187-267">Add multiple Polly handlers</span></span>

<span data-ttu-id="d0187-268">Polly 정책을 중첩하는 것은 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-268">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="d0187-269">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="d0187-269">In the preceding example:</span></span>

* <span data-ttu-id="d0187-270">두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-270">Two handlers are added.</span></span>
* <span data-ttu-id="d0187-271">첫 번째 처리기는 재시도 정책을 추가하기 위해 <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-271">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="d0187-272">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-272">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="d0187-273">두 번째 호출 `AddTransientHttpErrorPolicy`는 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-273">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="d0187-274">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-274">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="d0187-275">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-275">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="d0187-276">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-276">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="d0187-277">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="d0187-277">Add policies from the Polly registry</span></span>

<span data-ttu-id="d0187-278">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-278">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="d0187-279">다음 코드에서:</span><span class="sxs-lookup"><span data-stu-id="d0187-279">In the following code:</span></span>

* <span data-ttu-id="d0187-280">"regular" 정책과 "long" 정책이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-280">The "regular" and "long" policies are added.</span></span>
* <span data-ttu-id="d0187-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>가 레지스트리로부터 "regular" 정책과 "long" 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-281"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="d0187-282">`IHttpClientFactory` 및 Polly 통합에 대한 자세한 내용은 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-282">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="d0187-283">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="d0187-283">HttpClient and lifetime management</span></span>

<span data-ttu-id="d0187-284">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-284">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="d0187-285">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-285">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="d0187-286">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-286">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="d0187-287">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-287">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="d0187-288">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-288">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="d0187-289">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-289">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="d0187-290">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-290">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="d0187-291">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS(Domain Name System) 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-291">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="d0187-292">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-292">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="d0187-293">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-293">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="d0187-294">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 **않은** .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-294">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="d0187-295">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-295">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="d0187-296">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-296">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="d0187-297">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-297">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="d0187-298">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-298">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="d0187-299">IHttpClientFactory의 대안</span><span class="sxs-lookup"><span data-stu-id="d0187-299">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="d0187-300">DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-300">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="d0187-301">`HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-301">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="d0187-302">정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-302">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="d0187-303">수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-303">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="d0187-304">앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-304">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="d0187-305">DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-305">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="d0187-306">필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-306">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="d0187-307">위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-307">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="d0187-308">`SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-308">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="d0187-309">이와 같이 공유하면 소켓이 소모되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-309">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="d0187-310">오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-310">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="d0187-311">Cookies</span><span class="sxs-lookup"><span data-stu-id="d0187-311">Cookies</span></span>

<span data-ttu-id="d0187-312">풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-312">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="d0187-313">예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-313">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="d0187-314">cookie가 필요한 앱의 경우 다음 중 하나를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-314">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="d0187-315">자동 cookie 처리 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="d0187-315">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="d0187-316">`IHttpClientFactory` 방지</span><span class="sxs-lookup"><span data-stu-id="d0187-316">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="d0187-317"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>를 호출하여 자동 cookie 처리를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-317">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="d0187-318">로깅</span><span class="sxs-lookup"><span data-stu-id="d0187-318">Logging</span></span>

<span data-ttu-id="d0187-319">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-319">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="d0187-320">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-320">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="d0187-321">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-321">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="d0187-322">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-322">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="d0187-323">예를 들어, *MyNamedClient* 라는 클라이언트는 “System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler”의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-323">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="d0187-324">*LogicalHandler* 라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-324">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="d0187-325">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-325">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="d0187-326">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-326">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="d0187-327">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-327">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="d0187-328">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 “System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler”에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-328">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="d0187-329">요청의 경우 이는 요청이 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-329">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="d0187-330">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-330">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="d0187-331">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-331">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="d0187-332">여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-332">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="d0187-333">로그 범주에 클라이언트의 이름을 포함하면 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-333">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="d0187-334">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="d0187-334">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="d0187-335">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-335">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="d0187-336">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-336">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="d0187-337"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-337">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="d0187-338">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-338">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="d0187-339">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="d0187-339">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="d0187-340">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-340">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="d0187-341">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="d0187-341">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="d0187-342">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="d0187-342">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="d0187-343">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="d0187-343">In the following example:</span></span>

* <span data-ttu-id="d0187-344"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-344"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="d0187-345">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-345">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="d0187-346">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-346">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="d0187-347">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-347">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="d0187-348">헤더 전파 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d0187-348">Header propagation middleware</span></span>

<span data-ttu-id="d0187-349">헤더 전파는 들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파하는 ASP.NET Core 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-349">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="d0187-350">헤더 전파를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-350">To use header propagation:</span></span>

* <span data-ttu-id="d0187-351">[Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-351">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="d0187-352">`Startup`에서 미들웨어 및 `HttpClient`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-352">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="d0187-353">클라이언트는 아웃바운드 요청에 구성된 헤더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-353">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="d0187-354">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d0187-354">Additional resources</span></span>

* [<span data-ttu-id="d0187-355">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-355">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="d0187-356">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-356">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="d0187-357">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-357">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="d0187-358">.NET에서 JSON을 직렬화 및 역직렬화하는 방법</span><span class="sxs-lookup"><span data-stu-id="d0187-358">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d0187-359">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="d0187-359">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="d0187-360">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-360">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="d0187-361">이는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-361">It offers the following benefits:</span></span>

* <span data-ttu-id="d0187-362">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-362">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d0187-363">예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-363">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="d0187-364">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-364">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="d0187-365">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-365">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="d0187-366">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-366">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d0187-367">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-367">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d0187-368">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0187-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="d0187-369">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="d0187-369">Consumption patterns</span></span>

<span data-ttu-id="d0187-370">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-370">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="d0187-371">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="d0187-371">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="d0187-372">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-372">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="d0187-373">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-373">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="d0187-374">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-374">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="d0187-375">어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-375">None of them are strictly superior to another.</span></span> <span data-ttu-id="d0187-376">가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-376">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="d0187-377">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="d0187-377">Basic usage</span></span>

<span data-ttu-id="d0187-378">`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-378">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="d0187-379">일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-379">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d0187-380">`IHttpClientFactory`을(를) 사용하여 `HttpClient` 인스턴스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-380">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="d0187-381">이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-381">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="d0187-382">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-382">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="d0187-383">`HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-383">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="d0187-384">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-384">Named clients</span></span>

<span data-ttu-id="d0187-385">앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트** 를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-385">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="d0187-386">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-386">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="d0187-387">위의 코드에서는 `AddHttpClient`를 호출하여 *github* 이라는 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-387">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="d0187-388">이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-388">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="d0187-389">`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-389">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="d0187-390">명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-390">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="d0187-391">만들 클라이언트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-391">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="d0187-392">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-392">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="d0187-393">클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-393">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="d0187-394">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-394">Typed clients</span></span>

<span data-ttu-id="d0187-395">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="d0187-395">Typed clients:</span></span>

* <span data-ttu-id="d0187-396">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-396">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="d0187-397">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-397">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="d0187-398">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-398">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="d0187-399">예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-399">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="d0187-400">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-400">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="d0187-401">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-401">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="d0187-402">위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-402">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="d0187-403">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-403">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="d0187-404">`HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-404">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="d0187-405">`GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-405">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="d0187-406">형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-406">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="d0187-407">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-407">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="d0187-408">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-408">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="d0187-409">원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-409">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="d0187-410">형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-410">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="d0187-411">속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-411">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="d0187-412">위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-412">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="d0187-413">외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-413">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="d0187-414">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-414">Generated clients</span></span>

<span data-ttu-id="d0187-415">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-415">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="d0187-416">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-416">Refit is a REST library for .NET.</span></span> <span data-ttu-id="d0187-417">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-417">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="d0187-418">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-418">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="d0187-419">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-419">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="d0187-420">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-420">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="d0187-421">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-421">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="d0187-422">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d0187-422">Outgoing request middleware</span></span>

<span data-ttu-id="d0187-423">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-423">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="d0187-424">`IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-424">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="d0187-425">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-425">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d0187-426">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-426">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="d0187-427">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-427">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="d0187-428">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-428">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="d0187-429">처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-429">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="d0187-430">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-430">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="d0187-431">위의 코드에서는 기본 처리기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-431">The preceding code defines a basic handler.</span></span> <span data-ttu-id="d0187-432">`X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-432">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="d0187-433">헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-433">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="d0187-434">등록하는 동안 하나 이상의 처리기를 `HttpClient`의 구성에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-434">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="d0187-435">이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-435">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="d0187-436">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-436">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="d0187-437">`IHttpClientFactory`는 각 처리기에 대해 별도의 DI 범위를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-437">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="d0187-438">처리기는 모든 범위의 서비스에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-438">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="d0187-439">처리기가 삭제되면 처리기가 사용하는 서비스가 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-439">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="d0187-440">일단 등록되면 처리기의 형식을 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-440">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="d0187-441">여러 처리기를 실행해야 하는 순서에 따라 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-441">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="d0187-442">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-442">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="d0187-443">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-443">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="d0187-444">`HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-444">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="d0187-445">`IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-445">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="d0187-446">사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-446">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="d0187-447">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="d0187-447">Use Polly-based handlers</span></span>

<span data-ttu-id="d0187-448">`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-448">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="d0187-449">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-449">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="d0187-450">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-450">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="d0187-451">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-451">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="d0187-452">Polly 확장은:</span><span class="sxs-lookup"><span data-stu-id="d0187-452">The Polly extensions:</span></span>

* <span data-ttu-id="d0187-453">클라이언트에 Polly 기반 처리기 추가를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-453">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="d0187-454">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-454">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="d0187-455">이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-455">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="d0187-456">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="d0187-456">Handle transient faults</span></span>

<span data-ttu-id="d0187-457">가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-457">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="d0187-458">일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-458">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="d0187-459">이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-459">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="d0187-460">`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-460">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0187-461">이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-461">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="d0187-462">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-462">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="d0187-463">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-463">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="d0187-464">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="d0187-464">Dynamically select policies</span></span>

<span data-ttu-id="d0187-465">Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-465">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="d0187-466">이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-466">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="d0187-467">한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-467">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="d0187-468">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-468">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="d0187-469">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-469">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="d0187-470">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="d0187-470">Add multiple Polly handlers</span></span>

<span data-ttu-id="d0187-471">향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-471">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="d0187-472">위의 예제에서는 두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-472">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="d0187-473">첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-473">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="d0187-474">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-474">Failed requests are retried up to three times.</span></span> <span data-ttu-id="d0187-475">`AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-475">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="d0187-476">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-476">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="d0187-477">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-477">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="d0187-478">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-478">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="d0187-479">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="d0187-479">Add policies from the Polly registry</span></span>

<span data-ttu-id="d0187-480">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-480">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="d0187-481">레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-481">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="d0187-482">위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-482">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="d0187-483">레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-483">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="d0187-484">`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-484">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="d0187-485">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="d0187-485">HttpClient and lifetime management</span></span>

<span data-ttu-id="d0187-486">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-486">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="d0187-487">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-487">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="d0187-488">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-488">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="d0187-489">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-489">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="d0187-490">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-490">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="d0187-491">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-491">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="d0187-492">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-492">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="d0187-493">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-493">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="d0187-494">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-494">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="d0187-495">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-495">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="d0187-496">이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-496">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="d0187-497">클라이언트의 삭제는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-497">Disposal of the client isn't required.</span></span> <span data-ttu-id="d0187-498">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-498">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="d0187-499">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-499">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="d0187-500">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-500">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="d0187-501">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-501">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="d0187-502">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-502">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="d0187-503">IHttpClientFactory의 대안</span><span class="sxs-lookup"><span data-stu-id="d0187-503">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="d0187-504">DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-504">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="d0187-505">`HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-505">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="d0187-506">정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-506">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="d0187-507">수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-507">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="d0187-508">앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-508">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="d0187-509">DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-509">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="d0187-510">필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-510">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="d0187-511">위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-511">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="d0187-512">`SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-512">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="d0187-513">이와 같이 공유하면 소켓이 소모되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-513">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="d0187-514">오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-514">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="d0187-515">Cookies</span><span class="sxs-lookup"><span data-stu-id="d0187-515">Cookies</span></span>

<span data-ttu-id="d0187-516">풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-516">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="d0187-517">예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-517">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="d0187-518">cookie가 필요한 앱의 경우 다음 중 하나를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-518">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="d0187-519">자동 cookie 처리 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="d0187-519">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="d0187-520">`IHttpClientFactory` 방지</span><span class="sxs-lookup"><span data-stu-id="d0187-520">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="d0187-521"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>를 호출하여 자동 cookie 처리를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-521">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="d0187-522">로깅</span><span class="sxs-lookup"><span data-stu-id="d0187-522">Logging</span></span>

<span data-ttu-id="d0187-523">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-523">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="d0187-524">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-524">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="d0187-525">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-525">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="d0187-526">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-526">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="d0187-527">예를 들어, *MyNamedClient* 라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-527">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="d0187-528">*LogicalHandler* 라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-528">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="d0187-529">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-529">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="d0187-530">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-530">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="d0187-531">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-531">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="d0187-532">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-532">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="d0187-533">요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-533">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="d0187-534">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-534">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="d0187-535">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-535">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="d0187-536">예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-536">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="d0187-537">로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-537">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="d0187-538">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="d0187-538">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="d0187-539">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-539">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="d0187-540">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-540">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="d0187-541"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-541">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="d0187-542">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-542">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="d0187-543">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="d0187-543">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="d0187-544">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-544">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="d0187-545">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="d0187-545">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="d0187-546">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="d0187-546">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="d0187-547">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="d0187-547">In the following example:</span></span>

* <span data-ttu-id="d0187-548"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-548"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="d0187-549">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-549">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="d0187-550">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-550">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="d0187-551">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-551">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="d0187-552">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d0187-552">Additional resources</span></span>

* [<span data-ttu-id="d0187-553">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-553">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="d0187-554">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-554">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="d0187-555">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-555">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d0187-556">작성자: [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) 및 [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="d0187-556">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="d0187-557">앱에서 <xref:System.Net.Http.IHttpClientFactory>를 등록하여 <xref:System.Net.Http.HttpClient> 인스턴스를 구성하고 만드는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-557">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="d0187-558">이는 다음과 같은 이점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-558">It offers the following benefits:</span></span>

* <span data-ttu-id="d0187-559">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-559">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="d0187-560">예를 들어, [GitHub](https://github.com/)에 액세스하는 *github* 클라이언트를 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-560">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="d0187-561">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-561">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="d0187-562">`HttpClient`에서 위임 처리기를 통해 나가는 미들웨어의 개념을 체계화하고 Polly 기반 미들웨어에 대한 확장을 제공하여 이를 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-562">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="d0187-563">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-563">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="d0187-564">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-564">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="d0187-565">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0187-565">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0187-566">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="d0187-566">Prerequisites</span></span>

<span data-ttu-id="d0187-567">.NET Framework를 대상으로 하는 프로젝트는 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet 패키지를 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-567">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="d0187-568">.NET Core를 대상으로 하고 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하는 프로젝트는 이미 `Microsoft.Extensions.Http` 패키지를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-568">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="d0187-569">사용 패턴</span><span class="sxs-lookup"><span data-stu-id="d0187-569">Consumption patterns</span></span>

<span data-ttu-id="d0187-570">앱에서 `IHttpClientFactory`를 사용할 수 있는 몇 가지 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-570">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="d0187-571">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="d0187-571">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="d0187-572">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-572">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="d0187-573">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-573">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="d0187-574">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-574">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="d0187-575">어떤 방법도 다른 방법에 비해 절대적으로 우수하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-575">None of them are strictly superior to another.</span></span> <span data-ttu-id="d0187-576">가장 좋은 방법은 앱의 제약 조건에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-576">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="d0187-577">기본적인 사용 방법</span><span class="sxs-lookup"><span data-stu-id="d0187-577">Basic usage</span></span>

<span data-ttu-id="d0187-578">`IHttpClientFactory`는 `Startup.ConfigureServices` 메서드 내에서 `IServiceCollection`의 `AddHttpClient` 확장 메서드를 호출하여 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-578">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="d0187-579">일단 등록하고 나면 코드는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 사용하여 서비스를 주입할 수 있는 모든 곳에서 `IHttpClientFactory`를 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-579">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d0187-580">`IHttpClientFactory`을(를) 사용하여 `HttpClient` 인스턴스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-580">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="d0187-581">이러한 방식으로 `IHttpClientFactory`를 사용하는 것은 기존 앱을 리팩터링할 수 있는 좋은 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-581">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="d0187-582">`HttpClient`가 사용되는 방식에는 어떠한 영향도 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-582">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="d0187-583">`HttpClient` 인스턴스가 현재 만들어지는 위치에서 해당 코드를 <xref:System.Net.Http.IHttpClientFactory.CreateClient*>에 대한 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-583">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="d0187-584">명명된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-584">Named clients</span></span>

<span data-ttu-id="d0187-585">앱이 각각 다른 구성을 사용하는 많은 개별적인 `HttpClient`의 사용을 필요로 하는 경우 선택할 수 있는 방법은 **명명된 클라이언트** 를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-585">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="d0187-586">명명된 `HttpClient`에 대한 구성은 `Startup.ConfigureServices`에서 등록하는 동안 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-586">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="d0187-587">위의 코드에서는 `AddHttpClient`를 호출하여 *github* 이라는 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-587">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="d0187-588">이 클라이언트에는 일부 기본 구성&mdash;즉 GitHub API를 작동하는 데 필요한 기준 주소 및 두 개의 헤더가 적용되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-588">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="d0187-589">`CreateClient`를 호출할 때마다 `HttpClient`의 새 인스턴스를 만들고 구성 작업을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-589">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="d0187-590">명명된 클라이언트를 사용하기 위해서 `CreateClient`에 문자열 매개 변수를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-590">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="d0187-591">만들 클라이언트의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-591">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="d0187-592">위의 코드에서는 요청이 호스트 이름을 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-592">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="d0187-593">클라이언트에 대해 구성된 기본 주소를 사용하므로 경로만 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-593">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="d0187-594">형식화된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-594">Typed clients</span></span>

<span data-ttu-id="d0187-595">형식화된 클라이언트:</span><span class="sxs-lookup"><span data-stu-id="d0187-595">Typed clients:</span></span>

* <span data-ttu-id="d0187-596">문자열을 키로 사용할 필요가 없이 명명된 클라이언트와 동일한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-596">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="d0187-597">클라이언트를 사용할 때 IntelliSense 및 컴파일러 도움말을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-597">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="d0187-598">특정 `HttpClient`을 구성하고 상호 작용하기 위해 단일 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-598">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="d0187-599">예를 들어 단일 형식화된 클라이언트는 단일 백 엔드 엔드포인트에 사용될 수 있으며 해당 엔드포인트를 다루는 모든 논리를 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-599">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="d0187-600">DI로 작업하고 앱에서 필요할 경우 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-600">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="d0187-601">형식화된 클라이언트는 생성자에서 `HttpClient` 매개 변수를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-601">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="d0187-602">위의 코드에서는 구성이 형식화된 클라이언트로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-602">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="d0187-603">`HttpClient` 개체는 공용 속성으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-603">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="d0187-604">`HttpClient` 기능을 노출하는 API 특정 메서드를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-604">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="d0187-605">`GetAspNetDocsIssues` 메서드는 GitHub 리포지토리에서 공개된 최신 문제를 구문 분석하고 쿼리하는 데 필요한 코드를 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-605">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="d0187-606">형식화된 클라이언트를 등록하기 위해서 `Startup.ConfigureServices`에서 형식화된 클라이언트 클래스를 지정하여 제네릭 <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> 확장 메서드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-606">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="d0187-607">형식화된 클라이언트는 DI를 사용하여 일시적으로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-607">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="d0187-608">형식화된 클라이언트는 직접 주입되고 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-608">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="d0187-609">원한다면 형식화된 클라이언트의 생성자 대신 `Startup.ConfigureServices`에서 등록하는 동안 형식화된 클라이언트에 대한 구성을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-609">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="d0187-610">형식화된 클라이언트 내에서 `HttpClient`를 완전히 캡슐화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-610">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="d0187-611">속성으로 노출하는 대신 공용 메서드를 제공하여 내부적으로 `HttpClient` 인스턴스를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-611">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="d0187-612">위의 코드에서는 `HttpClient`가 전용 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-612">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="d0187-613">외부 호출을 하기 위한 모든 액세스는 `GetRepos` 메서드를 거칩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-613">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="d0187-614">생성된 클라이언트</span><span class="sxs-lookup"><span data-stu-id="d0187-614">Generated clients</span></span>

<span data-ttu-id="d0187-615">`IHttpClientFactory`는 [Refit](https://github.com/paulcbetts/refit)과 같은 다른 타사 라이브러리와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-615">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="d0187-616">Refit은 .NET용 REST 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-616">Refit is a REST library for .NET.</span></span> <span data-ttu-id="d0187-617">REST API를 라이브 인터페이스로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-617">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="d0187-618">인터페이스의 구현은 `HttpClient`를 사용하여 외부 HTTP를 호출하도록 `RestService`에 의해 동적으로 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-618">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="d0187-619">외부 API와 해당 응답을 나타내기 위한 인터페이스와 회신이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-619">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="d0187-620">구현을 생성하기 위해 Refit를 사용하여 형식화된 클라이언트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-620">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="d0187-621">DI 및 Refit에서 제공한 구현을 통해 필요한 곳에서 정의된 인터페이스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-621">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="d0187-622">나가는 요청 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d0187-622">Outgoing request middleware</span></span>

<span data-ttu-id="d0187-623">`HttpClient`에는 나가는 HTTP 요청을 위해 함께 연결될 수 있는 위임 처리기라는 개념이 이미 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-623">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="d0187-624">`IHttpClientFactory`를 사용하면 각 명명된 클라이언트에 적용할 처리기를 쉽게 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-624">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="d0187-625">나가는 요청 미들웨어 파이프라인을 만들기 위한 여러 처리기의 등록 및 연결을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-625">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="d0187-626">이러한 처리기 각각은 나가는 요청 전후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-626">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="d0187-627">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-627">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="d0187-628">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-628">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="d0187-629">처리기를 만들려면 <xref:System.Net.Http.DelegatingHandler>에서 파생되는 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-629">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="d0187-630">파이프라인의 다음 처리기로 요청을 전달하기 전에 코드를 실행하려면 `SendAsync` 메서드를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-630">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="d0187-631">위의 코드에서는 기본 처리기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-631">The preceding code defines a basic handler.</span></span> <span data-ttu-id="d0187-632">`X-API-KEY` 헤더가 요청에 포함되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-632">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="d0187-633">헤더가 누락된 경우 HTTP 호출을 방지하고 적합한 응답을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-633">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="d0187-634">등록하는 동안 하나 이상의 처리기를 `HttpClient`의 구성에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-634">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="d0187-635">이 작업은 <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>의 확장 메서드를 통해 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-635">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="d0187-636">위의 코드에서 `ValidateHeaderHandler`는 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-636">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="d0187-637">처리기는 범위가 지정되지 않은, 임시 서비스로 DI에 등록 **되어야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-637">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="d0187-638">처리기가 범위 지정 서비스로 등록되고 처리기가 종속된 모든 서비스는 삭제 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-638">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="d0187-639">처리기가 범위를 벗어나기 전에 처리기의 서비스를 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-639">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="d0187-640">처리기 서비스를 삭제하면 처리기가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-640">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="d0187-641">일단 등록되면 처리기 형식으로 전달하여 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*>를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-641">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="d0187-642">실행해야 하는 순서에 따라 여러 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-642">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="d0187-643">각 처리기는 최종 `HttpClientHandler`가 요청을 실행할 때까지 다음 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-643">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="d0187-644">다음 방법 중 하나를 사용하여 메시지 처리기와 요청별 상태를 공유하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-644">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="d0187-645">`HttpRequestMessage.Properties`를 사용하여 데이터를 처리기로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-645">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="d0187-646">`IHttpContextAccessor`를 사용하여 현재 요청에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-646">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="d0187-647">사용자 지정 `AsyncLocal` 스토리지 개체를 만들어 데이터를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-647">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="d0187-648">Polly 기반 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="d0187-648">Use Polly-based handlers</span></span>

<span data-ttu-id="d0187-649">`IHttpClientFactory`는 [Polly](https://github.com/App-vNext/Polly)라는 유명한 타사 라이브러리와 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-649">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="d0187-650">Polly는 .NET용 포괄적인 회복탄력성 및 일시적 오류 처리 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-650">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="d0187-651">개발자는 이를 사용하여 재시도, 회로 차단기, 시간 초과, 격벽 격리 및 대체(Fallback) 같은 정책을 유연하고 스레드로부터 안전한 방식으로 표현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-651">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="d0187-652">구성된 `HttpClient` 인스턴스를 통해 Polly 정책을 사용할 수 있도록 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-652">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="d0187-653">Polly 확장은:</span><span class="sxs-lookup"><span data-stu-id="d0187-653">The Polly extensions:</span></span>

* <span data-ttu-id="d0187-654">클라이언트에 Polly 기반 처리기 추가를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-654">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="d0187-655">[Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet 패키지를 설치한 후 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-655">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="d0187-656">이 패키지는 ASP.NET Core 공유 프레임워크에 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-656">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="d0187-657">일시적인 오류 처리</span><span class="sxs-lookup"><span data-stu-id="d0187-657">Handle transient faults</span></span>

<span data-ttu-id="d0187-658">가장 일반적인 오류는 외부 HTTP 호출이 일시적인 경우 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-658">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="d0187-659">일시적인 오류를 처리하기 위한 정책을 정의할 수 있는 `AddTransientHttpErrorPolicy`라는 편리한 확장 메서드가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-659">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="d0187-660">이 확장 메서드로 구성된 정책은 `HttpRequestException`, HTTP 5xx 응답 및 HTTP 408 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-660">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="d0187-661">`AddTransientHttpErrorPolicy` 확장은 `Startup.ConfigureServices` 내에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-661">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d0187-662">이 확장은 가능한 일시적 오류를 나타내는 오류를 처리하기 위해 구성된 `PolicyBuilder` 개체에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-662">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="d0187-663">위의 코드에서는 `WaitAndRetryAsync` 정책을 정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-663">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="d0187-664">실패한 요청은 최대 세 번까지 다시 시도되며 시도 간 600밀리초의 지연 간격을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-664">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="d0187-665">동적으로 정책 선택</span><span class="sxs-lookup"><span data-stu-id="d0187-665">Dynamically select policies</span></span>

<span data-ttu-id="d0187-666">Polly 기반 처리기를 추가하는 데 사용할 수 있는 추가 확장 메서드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-666">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="d0187-667">이러한 확장 중 하나는 여러 오버로드가 있는 `AddPolicyHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-667">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="d0187-668">한 오버로드는 적용할 정책을 정의할 때 요청을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-668">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="d0187-669">위의 코드에서는 나가는 요청이 HTTP GET인 경우 10초 시간 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-669">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="d0187-670">다른 HTTP 메서드의 경우 30초 시간 제한이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-670">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="d0187-671">여러 Polly 처리기 추가</span><span class="sxs-lookup"><span data-stu-id="d0187-671">Add multiple Polly handlers</span></span>

<span data-ttu-id="d0187-672">향상된 기능을 제공하기 위해 Polly 정책을 중첩하는 것이 일반적입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-672">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="d0187-673">위의 예제에서는 두 개의 처리기가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-673">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="d0187-674">첫 번째 처리기는 재시도 정책을 추가하기 위해 `AddTransientHttpErrorPolicy` 확장을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-674">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="d0187-675">실패한 요청은 최대 세 번까지 다시 시도됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-675">Failed requests are retried up to three times.</span></span> <span data-ttu-id="d0187-676">`AddTransientHttpErrorPolicy`에 대한 두 번째 호출은 회로 차단기 정책을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-676">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="d0187-677">5번의 시도가 순차적으로 실패하는 경우 추가적인 외부 요청은 30초 동안 차단됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-677">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="d0187-678">회로 차단기 정책은 상태를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-678">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="d0187-679">이 클라이언트를 통한 모든 호출은 동일한 회로 상태를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-679">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="d0187-680">Polly 레지스트리로부터 정책 추가</span><span class="sxs-lookup"><span data-stu-id="d0187-680">Add policies from the Polly registry</span></span>

<span data-ttu-id="d0187-681">정기적으로 사용되는 정책을 관리하는 방법은 정책을 한 번 정의하고 이를 `PolicyRegistry`에 등록하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-681">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="d0187-682">레지스트리의 정책을 사용하여 처리기를 추가할 수 있는 확장 메서드가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-682">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="d0187-683">위의 코드에서는 `PolicyRegistry`가 `ServiceCollection`에 추가될 때 두 가지 정책이 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-683">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="d0187-684">레지스트리의 정책을 사용하기 위해서는 적용할 정책의 이름을 전달하여 `AddPolicyHandlerFromRegistry` 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-684">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="d0187-685">`IHttpClientFactory` 및 Polly 통합에 대한 추가 정보는 [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory)에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-685">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="d0187-686">HttpClient 및 수명 관리</span><span class="sxs-lookup"><span data-stu-id="d0187-686">HttpClient and lifetime management</span></span>

<span data-ttu-id="d0187-687">`IHttpClientFactory`에서 `CreateClient`가 호출될 때마다 새로운 `HttpClient` 인스턴스가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-687">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="d0187-688">명명된 클라이언트마다 <xref:System.Net.Http.HttpMessageHandler>가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-688">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="d0187-689">팩터리는 `HttpMessageHandler` 인스턴스의 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-689">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="d0187-690">`IHttpClientFactory`는 리소스 사용을 줄이기 위해 팩터리에서 만든 `HttpMessageHandler` 인스턴스를 풀링합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-690">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="d0187-691">수명이 만료되지 않은 경우, 새 `HttpClient` 인스턴스를 만들 때 풀에서 `HttpMessageHandler` 인스턴스가 재사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-691">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="d0187-692">일반적으로 각 처리기는 자체적인 기본 HTTP 연결을 관리하므로 처리기의 풀링이 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-692">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="d0187-693">필요한 것보다 많은 처리기를 만들면 연결 지연이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-693">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="d0187-694">또한 일부 처리기는 무한정으로 연결을 열어 놓아 처리기가 DNS 변경에 대응하는 것을 막을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-694">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="d0187-695">기본 처리기 수명은 2분입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-695">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="d0187-696">명명된 클라이언트별 기준으로 기본값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-696">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="d0187-697">이를 재정의하려면 클라이언트를 만들 때 반환되는 `IHttpClientBuilder`에서 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-697">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="d0187-698">클라이언트의 삭제는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-698">Disposal of the client isn't required.</span></span> <span data-ttu-id="d0187-699">삭제는 나가는 요청을 취소하고 <xref:System.IDisposable.Dispose*>를 호출한 후에는 지정된 `HttpClient` 인스턴스가 사용될 수 없도록 보장합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-699">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="d0187-700">`IHttpClientFactory`는 `HttpClient` 인스턴스에서 사용되는 리소스를 추적하고 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-700">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="d0187-701">`HttpClient` 인스턴스는 일반적으로 삭제가 필요하지 않은 .NET 개체로 간주할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-701">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="d0187-702">장기간 단일 `HttpClient` 인스턴스를 활성 상태로 유지하는 것은 `IHttpClientFactory`가 등장하기 전에 사용되던 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-702">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="d0187-703">이 패턴은 `IHttpClientFactory`로 마이그레이션한 이후에는 불필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-703">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="d0187-704">IHttpClientFactory의 대안</span><span class="sxs-lookup"><span data-stu-id="d0187-704">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="d0187-705">DI 지원 앱에서 `IHttpClientFactory`을(를) 사용하면 다음이 방지됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-705">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="d0187-706">`HttpMessageHandler` 인스턴스를 풀링하여 리소스 소모 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-706">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="d0187-707">정기적으로 `HttpMessageHandler` 인스턴스를 순환하여 오래된 DNS 문제가 발생했습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-707">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="d0187-708">수명이 긴 <xref:System.Net.Http.SocketsHttpHandler> 인스턴스를 사용하여 위의 문제를 해결하는 다른 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-708">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="d0187-709">앱 시작 시 `SocketsHttpHandler`의 인스턴스를 만들고 앱 수명 동안 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-709">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="d0187-710">DNS 새로 고침 시간에 따라 적절한 값으로 <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime>을(를) 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-710">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="d0187-711">필요에 따라 `new HttpClient(handler, disposeHandler: false)`을(를) 사용하여 `HttpClient` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-711">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="d0187-712">위의 방법은 비슷한 방식으로 `IHttpClientFactory`에서 해결하는 리소스 관리 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-712">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="d0187-713">`SocketsHttpHandler`은(는) `HttpClient` 인스턴스 간에 연결을 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-713">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="d0187-714">이와 같이 공유하면 소켓이 소모되지 않도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-714">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="d0187-715">오래된 DNS 문제를 방지하기 위해 `SocketsHttpHandler`에서 `PooledConnectionLifetime`에 따라 연결을 순환합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-715">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="d0187-716">Cookies</span><span class="sxs-lookup"><span data-stu-id="d0187-716">Cookies</span></span>

<span data-ttu-id="d0187-717">풀링된 `HttpMessageHandler` 인스턴스는 `CookieContainer` 개체를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-717">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="d0187-718">예상치 못한 `CookieContainer` 개체 공유로 잘못된 코드가 발생하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-718">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="d0187-719">cookie가 필요한 앱의 경우 다음 중 하나를 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="d0187-719">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="d0187-720">자동 cookie 처리 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="d0187-720">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="d0187-721">`IHttpClientFactory` 방지</span><span class="sxs-lookup"><span data-stu-id="d0187-721">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="d0187-722"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*>를 호출하여 자동 cookie 처리를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-722">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="d0187-723">로깅</span><span class="sxs-lookup"><span data-stu-id="d0187-723">Logging</span></span>

<span data-ttu-id="d0187-724">`IHttpClientFactory`을 통해 만든 클라이언트는 모든 요청에 대한 로그 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-724">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="d0187-725">기본 로그 메시지를 보려면 로깅 구성에서 적절한 정보 수준을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-725">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="d0187-726">요청 헤더의 로깅 등과 같은 추가 로깅은 추적 수준에서만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-726">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="d0187-727">각 클라이언트에 사용되는 로그 범주는 클라이언트의 이름을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-727">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="d0187-728">예를 들어, *MyNamedClient* 라는 클라이언트는 `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`의 범주를 사용하여 메시지를 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-728">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="d0187-729">*LogicalHandler* 라는 접미사가 붙은 메시지는 요청 처리기 파이프라인 외부에서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-729">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="d0187-730">요청 시 파이프라인의 다른 모든 처리기에서 이를 처리하기 전에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-730">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="d0187-731">응답 시 다른 모든 파이프라인 처리기가 응답을 받은 후에 메시지가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-731">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="d0187-732">로깅은 요청 처리기 파이프라인 내부에서도 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-732">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="d0187-733">*MyNamedClient* 예제에서 해당 메시지는 로그 범주 `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-733">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="d0187-734">요청의 경우 이는 요청이 네트워크에서 전송되기 직전 및 다른 모든 처리기가 실행된 후에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-734">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="d0187-735">응답 시 이 로깅은 처리기 파이프라인을 통해 응답이 다시 전달되기 전의 응답 상태를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-735">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="d0187-736">파이프라인 외부 및 내부에서 로깅을 사용하도록 설정하면 다른 파이프라인 처리기가 수행한 변경 내용을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-736">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="d0187-737">예를 들어 여기에는 요청 헤더 또는 응답 상태 코드에 대한 변경이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-737">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="d0187-738">로그 범주에 클라이언트의 이름을 포함하면 필요한 경우 명명된 특정 클라이언트에 대한 로그 필터링을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-738">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="d0187-739">HttpMessageHandler 구성</span><span class="sxs-lookup"><span data-stu-id="d0187-739">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="d0187-740">클라이언트가 사용하는 내부 `HttpMessageHandler`의 구성을 제어해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-740">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="d0187-741">`IHttpClientBuilder`는 명명된 또는 형식화된 클라이언트를 추가할 때 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-741">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="d0187-742"><xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> 확장 메서드는 대리자를 정의하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-742">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="d0187-743">대리자는 해당 클라이언트가 사용하는 기본 `HttpMessageHandler`을 만들고 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-743">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="d0187-744">콘솔 앱에서 IHttpClientFactory 사용</span><span class="sxs-lookup"><span data-stu-id="d0187-744">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="d0187-745">콘솔 앱에서 프로젝트에 다음 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-745">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="d0187-746">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="d0187-746">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="d0187-747">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="d0187-747">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="d0187-748">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="d0187-748">In the following example:</span></span>

* <span data-ttu-id="d0187-749"><xref:System.Net.Http.IHttpClientFactory>는 [제너릭 호스트의](xref:fundamentals/host/generic-host) 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-749"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="d0187-750">`MyService`에서는 `HttpClient`를 만드는 데 사용하는 서비스에서 클라이언트 팩터리 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-750">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="d0187-751">`HttpClient`는 웹 페이지를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-751">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="d0187-752">`Main`을 통해서는 서비스의 `GetPage` 메서드를 실행하고 웹 페이지 콘텐츠의 처음 500자를 콘솔에 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-752">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="d0187-753">헤더 전파 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d0187-753">Header propagation middleware</span></span>

<span data-ttu-id="d0187-754">헤더 전파는 들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파하는 커뮤니티 지원 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-754">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="d0187-755">헤더 전파를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-755">To use header propagation:</span></span>

* <span data-ttu-id="d0187-756">[HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation) 패키지의 커뮤니티 지원 포트를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-756">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="d0187-757">ASP.NET Core 3.1 이상은 [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-757">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="d0187-758">`Startup`에서 미들웨어 및 `HttpClient`를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-758">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="d0187-759">클라이언트는 아웃바운드 요청에 구성된 헤더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d0187-759">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="d0187-760">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d0187-760">Additional resources</span></span>

* [<span data-ttu-id="d0187-761">HttpClientFactory를 사용하여 복원력 있는 HTTP 요청 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-761">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="d0187-762">HttpClientFactory 및 Polly 정책을 통해 지수 백오프를 사용하여 HTTP 호출 다시 시도 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-762">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="d0187-763">회로 차단기 패턴 구현</span><span class="sxs-lookup"><span data-stu-id="d0187-763">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
