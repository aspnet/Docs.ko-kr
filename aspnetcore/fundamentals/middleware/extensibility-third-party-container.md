---
title: ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화
author: rick-anderson
description: ASP.NET Core에서 팩터리 기반 활성화 및 타사 컨테이너를 사용하여 강력한 형식의 미들웨어를 사용하는 방법을 배웁니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057767"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="46187-103">ASP.NET Core에서 타사 컨테이너를 사용한 미들웨어 활성화</span><span class="sxs-lookup"><span data-stu-id="46187-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46187-104">이 문서에서는 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 및 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 타사 컨테이너를 사용한 [미들웨어](xref:fundamentals/middleware/index) 활성화를 위한 확장 지점으로 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="46187-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="46187-105">`IMiddlewareFactory` 및 `IMiddleware`에 대한 정보는 <xref:fundamentals/middleware/extensibility>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="46187-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="46187-106">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="46187-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="46187-107">샘플 앱은 `IMiddlewareFactory` 구현 `SimpleInjectorMiddlewareFactory`를 사용한 미들웨어 활성화를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="46187-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="46187-108">이 샘플에서는 [간단한 인젝터](https://simpleinjector.org) DI(종속성 주입) 컨테이너를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="46187-109">이 샘플의 미들웨어 구현은 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="46187-110">이 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="46187-111">이 샘플 앱에서는 순전히 예시 목적으로 [간단한 인젝터](https://github.com/simpleinjector/SimpleInjector)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="46187-112">간단한 인젝터의 사용은 보증하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="46187-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="46187-113">간단한 인젝터 설명서 및 GitHub 문제에 설명된 미들웨어 활성화 방법은 간단한 인젝터의 관리자에서 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="46187-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="46187-114">자세한 내용은 [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html) 및 [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="46187-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="46187-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="46187-115">IMiddlewareFactory</span></span>

<span data-ttu-id="46187-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="46187-117">샘플 앱에서는 미들웨어 팩터리를 구현하여 `SimpleInjectorActivatedMiddleware` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="46187-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="46187-118">미들웨어 팩터리는 간단한 인젝터 컨테이너를 사용하여 미들웨어를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="46187-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="46187-119">IMiddleware</span></span>

<span data-ttu-id="46187-120"><xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="46187-121">`IMiddlewareFactory` 구현에 의해 활성화된 미들웨어( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="46187-121">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="46187-122">미들웨어에 대한 확장( *Middleware/MiddlewareExtensions.cs* )이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="46187-122">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="46187-123">`Startup.ConfigureServices`는 여러 작업을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="46187-124">간단한 인젝터 컨테이너를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="46187-125">팩터리와 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="46187-126">간단한 인젝터 컨테이너에서 앱의 데이터베이스 컨텍스트를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="46187-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="46187-127">미들웨어는 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="46187-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="46187-128">이 문서에서는 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 및 <xref:Microsoft.AspNetCore.Http.IMiddleware>를 타사 컨테이너를 사용한 [미들웨어](xref:fundamentals/middleware/index) 활성화를 위한 확장 지점으로 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="46187-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="46187-129">`IMiddlewareFactory` 및 `IMiddleware`에 대한 정보는 <xref:fundamentals/middleware/extensibility>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="46187-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="46187-130">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="46187-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="46187-131">샘플 앱은 `IMiddlewareFactory` 구현 `SimpleInjectorMiddlewareFactory`를 사용한 미들웨어 활성화를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="46187-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="46187-132">이 샘플에서는 [간단한 인젝터](https://simpleinjector.org) DI(종속성 주입) 컨테이너를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="46187-133">이 샘플의 미들웨어 구현은 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="46187-134">이 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="46187-135">이 샘플 앱에서는 순전히 예시 목적으로 [간단한 인젝터](https://github.com/simpleinjector/SimpleInjector)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="46187-136">간단한 인젝터의 사용은 보증하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="46187-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="46187-137">간단한 인젝터 설명서 및 GitHub 문제에 설명된 미들웨어 활성화 방법은 간단한 인젝터의 관리자에서 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="46187-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="46187-138">자세한 내용은 [간단한 인젝터 설명서](https://simpleinjector.readthedocs.io/en/latest/index.html) 및 [간단한 인젝터 GitHub 리포지토리](https://github.com/simpleinjector/SimpleInjector)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="46187-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="46187-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="46187-139">IMiddlewareFactory</span></span>

<span data-ttu-id="46187-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>는 미들웨어를 만드는 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="46187-141">샘플 앱에서는 미들웨어 팩터리를 구현하여 `SimpleInjectorActivatedMiddleware` 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="46187-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="46187-142">미들웨어 팩터리는 간단한 인젝터 컨테이너를 사용하여 미들웨어를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="46187-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="46187-143">IMiddleware</span></span>

<span data-ttu-id="46187-144"><xref:Microsoft.AspNetCore.Http.IMiddleware>는 앱의 요청 파이프라인에 대한 미들웨어를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="46187-145">`IMiddlewareFactory` 구현에 의해 활성화된 미들웨어( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span><span class="sxs-lookup"><span data-stu-id="46187-145">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="46187-146">미들웨어에 대한 확장( *Middleware/MiddlewareExtensions.cs* )이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="46187-146">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="46187-147">`Startup.ConfigureServices`는 여러 작업을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="46187-148">간단한 인젝터 컨테이너를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="46187-149">팩터리와 미들웨어를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="46187-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="46187-150">간단한 인젝터 컨테이너에서 앱의 데이터베이스 컨텍스트를 사용할 수 있게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="46187-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="46187-151">미들웨어는 `Startup.Configure`의 요청 처리 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="46187-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="46187-152">추가 자료</span><span class="sxs-lookup"><span data-stu-id="46187-152">Additional resources</span></span>

* [<span data-ttu-id="46187-153">미들웨어</span><span class="sxs-lookup"><span data-stu-id="46187-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="46187-154">팩터리 기반 미들웨어 활성화</span><span class="sxs-lookup"><span data-stu-id="46187-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="46187-155">간단한 인젝터 GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="46187-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="46187-156">간단한 인젝터 설명서</span><span class="sxs-lookup"><span data-stu-id="46187-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
