---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: 06f55647ba2bb41152e16bb054e098abbe157cb8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059366"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="415ee-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="415ee-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="415ee-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="415ee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="415ee-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="415ee-106">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-106">Each component:</span></span>

* <span data-ttu-id="415ee-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="415ee-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="415ee-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="415ee-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="415ee-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="415ee-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="415ee-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어* 라고 하며, *미들웨어 구성 요소* 라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-113">These reusable classes and in-line anonymous methods are *middleware* , also called *middleware components* .</span></span> <span data-ttu-id="415ee-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="415ee-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="415ee-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="415ee-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="415ee-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="415ee-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="415ee-119">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="415ee-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="415ee-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="415ee-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="415ee-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="415ee-127">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="415ee-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="415ee-129"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="415ee-130">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="415ee-131">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="415ee-132">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="415ee-133">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline* .</span></span> <span data-ttu-id="415ee-134">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="415ee-135">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어* 로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="415ee-136">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="415ee-137">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="415ee-138">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="415ee-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="415ee-139">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="415ee-140">예를 들어 [헤더 및 상태 코드를 설정하면 예외를 throw합니다](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span><span class="sxs-lookup"><span data-stu-id="415ee-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="415ee-141">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="415ee-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="415ee-142">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-142">May cause a protocol violation.</span></span> <span data-ttu-id="415ee-143">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="415ee-144">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-144">May corrupt the body format.</span></span> <span data-ttu-id="415ee-145">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="415ee-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="415ee-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 `next` 매개 변수를 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="415ee-148">첫 번째 `Run` 대리자는 항상 터미널이며 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="415ee-149">`Run`이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-149">`Run` is a convention.</span></span> <span data-ttu-id="415ee-150">일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="415ee-151">위 예제에서 `Run` 대리자는 응답에 `"Hello from 2nd delegate."`를 쓴 다음 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="415ee-152">`Run` 대리자 뒤에 추가된 다른 `Use` 또는 `Run` 대리자는 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="415ee-153">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="415ee-153">Middleware order</span></span>

<span data-ttu-id="415ee-154">다음 다이어그램은 ASP.NET Core MVC 및 Razor Pages 앱의 전체 요청 처리 파이프라인을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="415ee-155">일반적인 앱에서 기존 미들웨어의 순서가 지정되는 방식과 사용자 지정 미들웨어가 추가되는 위치를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="415ee-156">사용자는 원하는 대로 기존 미들웨어의 순서를 바꾸거나 시나리오의 필요에 따라 새 사용자 지정 미들웨어를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 미들웨어 파이프라인](index/_static/middleware-pipeline.svg)

<span data-ttu-id="415ee-158">앞에 나온 다이어그램의 **엔드포인트** 미들웨어는 해당 앱 형식&mdash;MVC 또는 Razor Pages에 따라 필터 파이프라인을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 필터 파이프라인](index/_static/mvc-endpoint.svg)

<span data-ttu-id="415ee-160">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="415ee-161">순서는 보안, 성능 및 기능에 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="415ee-162">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-162">The following `Startup.Configure` method adds security-related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="415ee-163">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="415ee-163">In the preceding code:</span></span>

* <span data-ttu-id="415ee-164">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="415ee-165">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="415ee-166">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="415ee-166">For example:</span></span>
  * <span data-ttu-id="415ee-167">`UseCors`, `UseAuthentication`, `UseAuthorization`은 표시된 순서를 지켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="415ee-168">현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="415ee-169">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-169">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="415ee-170">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="415ee-170">Exception/error handling</span></span>
   * <span data-ttu-id="415ee-171">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="415ee-171">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="415ee-172">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-172">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="415ee-173">데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-173">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="415ee-174">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="415ee-174">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="415ee-175">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-175">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="415ee-176">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-176">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="415ee-177">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-177">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="415ee-178">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-178">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="415ee-179">Cookie 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-179">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="415ee-180">요청을 라우팅하도록 미들웨어(<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) 라우팅.</span><span class="sxs-lookup"><span data-stu-id="415ee-180">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="415ee-181">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-181">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="415ee-182">권한 부여 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-182">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="415ee-183">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-183">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="415ee-184">앱이 세션 상태를 사용하는 경우에는 Cookie 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-184">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="415ee-185">엔드포인트 라우팅 미들웨어(<xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>를 포함하는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-185">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="415ee-186">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-186">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="415ee-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-187"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="415ee-188">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-188">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="415ee-189">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-189">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="415ee-190">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="415ee-190">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="415ee-191">*wwwroot* 에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-191">Any files served by Static File Middleware, including those under *wwwroot* , are publicly available.</span></span> <span data-ttu-id="415ee-192">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-192">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="415ee-193">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-193">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="415ee-194">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-194">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="415ee-195">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-195">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="415ee-196">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-196">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="415ee-197">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-197">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="415ee-198">Razor Pages 응답을 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-198">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="415ee-199">SPA(단일 페이지 애플리케이션)의 경우 SPA 미들웨어 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A>는 일반적으로 미들웨어 파이프라인에서 마지막으로 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-199">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="415ee-200">SPA 미들웨어가 마지막으로 나오는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-200">The SPA middleware comes last:</span></span>

* <span data-ttu-id="415ee-201">다른 모든 미들웨어가 일치하는 요청에 먼저 응답하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-201">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="415ee-202">클라이언트 쪽 라우팅이 있는 SPA가 서버 앱에서 인식할 수 없는 모든 경로에 대해 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-202">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="415ee-203">SPA에 대한 자세한 내용은 [React](xref:spa/react) 및 [Angular](xref:spa/angular) 프로젝트 템플릿 관련 가이드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-203">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="415ee-204">전달된 헤더 미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="415ee-204">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="415ee-205">미들웨어 파이프라인 분기</span><span class="sxs-lookup"><span data-stu-id="415ee-205">Branch the middleware pipeline</span></span>

<span data-ttu-id="415ee-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-206"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="415ee-207">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-207">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="415ee-208">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-208">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="415ee-209">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-209">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="415ee-210">요청</span><span class="sxs-lookup"><span data-stu-id="415ee-210">Request</span></span>             | <span data-ttu-id="415ee-211">응답</span><span class="sxs-lookup"><span data-stu-id="415ee-211">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="415ee-212">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="415ee-212">localhost:1234</span></span>      | <span data-ttu-id="415ee-213">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="415ee-213">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="415ee-214">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="415ee-214">localhost:1234/map1</span></span> | <span data-ttu-id="415ee-215">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="415ee-215">Map Test 1</span></span>                   |
| <span data-ttu-id="415ee-216">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="415ee-216">localhost:1234/map2</span></span> | <span data-ttu-id="415ee-217">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="415ee-217">Map Test 2</span></span>                   |
| <span data-ttu-id="415ee-218">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="415ee-218">localhost:1234/map3</span></span> | <span data-ttu-id="415ee-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="415ee-219">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="415ee-220">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-220">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="415ee-221">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-221">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="415ee-222">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-222">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="415ee-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-223"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="415ee-224">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-224">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="415ee-225">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-225">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="415ee-226">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-226">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="415ee-227">요청</span><span class="sxs-lookup"><span data-stu-id="415ee-227">Request</span></span>                       | <span data-ttu-id="415ee-228">응답</span><span class="sxs-lookup"><span data-stu-id="415ee-228">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="415ee-229">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="415ee-229">localhost:1234</span></span>                | <span data-ttu-id="415ee-230">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="415ee-230">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="415ee-231">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="415ee-231">localhost:1234/?branch=master</span></span> | <span data-ttu-id="415ee-232">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="415ee-232">Branch used = master</span></span>         |

<span data-ttu-id="415ee-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A>도 지정된 조건자의 결과를 기준으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-233"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="415ee-234">`MapWhen`과 달리, 이 분기는 단락을 수행하거나 터미널 미들웨어를 포함하지 않는 경우 기본 파이프라인에 다시 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-234">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="415ee-235">위 예제에서는 “Hello from main pipeline.” 응답이</span><span class="sxs-lookup"><span data-stu-id="415ee-235">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="415ee-236">모든 요청에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-236">is written for all requests.</span></span> <span data-ttu-id="415ee-237">요청에 쿼리 문자열 변수 `branch`가 포함되어 있으면, 기본 파이프라인이 다시 연결되기 전에 변수 값이 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-237">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="415ee-238">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="415ee-238">Built-in middleware</span></span>

<span data-ttu-id="415ee-239">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-239">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="415ee-240">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-240">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="415ee-241">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-241">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware* .</span></span> <span data-ttu-id="415ee-242">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-242">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="415ee-243">미들웨어</span><span class="sxs-lookup"><span data-stu-id="415ee-243">Middleware</span></span> | <span data-ttu-id="415ee-244">설명</span><span class="sxs-lookup"><span data-stu-id="415ee-244">Description</span></span> | <span data-ttu-id="415ee-245">순서</span><span class="sxs-lookup"><span data-stu-id="415ee-245">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="415ee-246">인증</span><span class="sxs-lookup"><span data-stu-id="415ee-246">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="415ee-247">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-247">Provides authentication support.</span></span> | <span data-ttu-id="415ee-248">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="415ee-248">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="415ee-249">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="415ee-249">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="415ee-250">권한 부여</span><span class="sxs-lookup"><span data-stu-id="415ee-250">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="415ee-251">권한 부여 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-251">Provides authorization support.</span></span> | <span data-ttu-id="415ee-252">인증 미들웨어 바로 뒤에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-252">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="415ee-253">Cookie 정책</span><span class="sxs-lookup"><span data-stu-id="415ee-253">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="415ee-254">개인 정보 저장과 관련한 사용자의 동의를 추적하고 cookie 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-254">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="415ee-255">cookie를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="415ee-255">Before middleware that issues cookies.</span></span> <span data-ttu-id="415ee-256">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="415ee-256">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="415ee-257">CORS</span><span class="sxs-lookup"><span data-stu-id="415ee-257">CORS</span></span>](xref:security/cors) | <span data-ttu-id="415ee-258">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-258">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="415ee-259">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-259">Before components that use CORS.</span></span> <span data-ttu-id="415ee-260">현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-260">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="415ee-261">진단</span><span class="sxs-lookup"><span data-stu-id="415ee-261">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="415ee-262">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="415ee-262">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="415ee-263">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-263">Before components that generate errors.</span></span> <span data-ttu-id="415ee-264">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-264">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="415ee-265">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="415ee-265">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="415ee-266">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-266">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="415ee-267">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="415ee-267">Before components that consume the updated fields.</span></span> <span data-ttu-id="415ee-268">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="415ee-268">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="415ee-269">상태 검사</span><span class="sxs-lookup"><span data-stu-id="415ee-269">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="415ee-270">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-270">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="415ee-271">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-271">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="415ee-272">헤더 전파</span><span class="sxs-lookup"><span data-stu-id="415ee-272">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="415ee-273">들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-273">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="415ee-274">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="415ee-274">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="415ee-275">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-275">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="415ee-276">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-276">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="415ee-277">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="415ee-277">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="415ee-278">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-278">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="415ee-279">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-279">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="415ee-280">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="415ee-280">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="415ee-281">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-281">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="415ee-282">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="415ee-282">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="415ee-283">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="415ee-283">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="415ee-284">MVC</span><span class="sxs-lookup"><span data-stu-id="415ee-284">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="415ee-285">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-285">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="415ee-286">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-286">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="415ee-287">OWIN</span><span class="sxs-lookup"><span data-stu-id="415ee-287">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="415ee-288">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-288">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="415ee-289">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-289">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="415ee-290">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="415ee-290">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="415ee-291">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-291">Provides support for caching responses.</span></span> | <span data-ttu-id="415ee-292">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-292">Before components that require caching.</span></span> <span data-ttu-id="415ee-293">`UseCORS`는 `UseResponseCaching` 앞에 와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-293">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="415ee-294">응답 압축</span><span class="sxs-lookup"><span data-stu-id="415ee-294">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="415ee-295">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-295">Provides support for compressing responses.</span></span> | <span data-ttu-id="415ee-296">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-296">Before components that require compression.</span></span> |
| [<span data-ttu-id="415ee-297">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="415ee-297">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="415ee-298">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-298">Provides localization support.</span></span> | <span data-ttu-id="415ee-299">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-299">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="415ee-300">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="415ee-300">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="415ee-301">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-301">Defines and constrains request routes.</span></span> | <span data-ttu-id="415ee-302">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="415ee-302">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="415ee-303">SPA</span><span class="sxs-lookup"><span data-stu-id="415ee-303">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="415ee-304">SPA(단일 페이지 애플리케이션)의 기반 페이지를 반환하여 이 시점부터 미들웨어 체인의 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-304">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="415ee-305">정적 파일, MVC 작업 등을 처리하는 다른 미들웨어가 우선할 수 있도록 이 미들웨어는 체인에서 늦게 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-305">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="415ee-306">세션</span><span class="sxs-lookup"><span data-stu-id="415ee-306">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="415ee-307">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-307">Provides support for managing user sessions.</span></span> | <span data-ttu-id="415ee-308">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-308">Before components that require Session.</span></span> | 
| [<span data-ttu-id="415ee-309">정적 파일</span><span class="sxs-lookup"><span data-stu-id="415ee-309">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="415ee-310">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-310">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="415ee-311">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-311">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="415ee-312">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="415ee-312">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="415ee-313">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-313">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="415ee-314">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-314">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="415ee-315">WebSockets</span><span class="sxs-lookup"><span data-stu-id="415ee-315">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="415ee-316">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-316">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="415ee-317">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-317">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="415ee-318">추가 자료</span><span class="sxs-lookup"><span data-stu-id="415ee-318">Additional resources</span></span>

* <span data-ttu-id="415ee-319">[수명 및 등록 옵션](xref:fundamentals/dependency-injection#lifetime-and-registration-options) ‘범위가 지정되고’, ‘일시적인’ *singleton* 수명 서비스를 사용하는 미들웨어의 전체 샘플이 포함되어 있습니다. </span><span class="sxs-lookup"><span data-stu-id="415ee-319">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* , *transient* , and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="415ee-320">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="415ee-320">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="415ee-321">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-321">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="415ee-322">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-322">Each component:</span></span>

* <span data-ttu-id="415ee-323">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-323">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="415ee-324">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-324">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="415ee-325">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-325">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="415ee-326">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-326">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="415ee-327">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-327">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="415ee-328">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-328">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="415ee-329">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어* 라고 하며, *미들웨어 구성 요소* 라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-329">These reusable classes and in-line anonymous methods are *middleware* , also called *middleware components* .</span></span> <span data-ttu-id="415ee-330">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-330">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="415ee-331">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-331">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="415ee-332"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-332"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="415ee-333">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="415ee-333">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="415ee-334">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-334">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="415ee-335">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-335">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="415ee-336">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-336">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="415ee-340">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-340">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="415ee-341">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-341">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="415ee-342">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-342">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="415ee-343">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-343">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="415ee-344">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-344">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="415ee-345">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-345">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="415ee-346"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-346">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="415ee-347">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-347">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="415ee-348">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-348">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="415ee-349">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-349">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="415ee-350">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-350">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline* .</span></span> <span data-ttu-id="415ee-351">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-351">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="415ee-352">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어* 로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-352">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="415ee-353">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-353">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="415ee-354">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-354">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="415ee-355">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="415ee-355">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="415ee-356">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-356">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="415ee-357">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-357">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="415ee-358">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="415ee-358">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="415ee-359">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-359">May cause a protocol violation.</span></span> <span data-ttu-id="415ee-360">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-360">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="415ee-361">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-361">May corrupt the body format.</span></span> <span data-ttu-id="415ee-362">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-362">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="415ee-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-363"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="415ee-364">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="415ee-364">Middleware order</span></span>

<span data-ttu-id="415ee-365">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-365">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="415ee-366">순서는 보안, 성능 및 기능에 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-366">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="415ee-367">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-367">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="415ee-368">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="415ee-368">In the preceding code:</span></span>

* <span data-ttu-id="415ee-369">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-369">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="415ee-370">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-370">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="415ee-371">예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-371">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="415ee-372">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-372">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="415ee-373">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="415ee-373">Exception/error handling</span></span>
   * <span data-ttu-id="415ee-374">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="415ee-374">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="415ee-375">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-375">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="415ee-376">데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-376">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="415ee-377">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="415ee-377">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="415ee-378">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-378">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="415ee-379">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-379">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="415ee-380">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-380">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="415ee-381">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-381">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="415ee-382">Cookie 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-382">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="415ee-383">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-383">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="415ee-384">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-384">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="415ee-385">앱이 세션 상태를 사용하는 경우에는 Cookie 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-385">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="415ee-386">MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>)는 MVC를 요청 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-386">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="415ee-387">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-387">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="415ee-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-388"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="415ee-389">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-389">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="415ee-390">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-390">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="415ee-391">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다** .</span><span class="sxs-lookup"><span data-stu-id="415ee-391">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="415ee-392">*wwwroot* 에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-392">Any files served by Static File Middleware, including those under *wwwroot* , are publicly available.</span></span> <span data-ttu-id="415ee-393">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-393">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="415ee-394">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-394">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="415ee-395">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-395">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="415ee-396">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-396">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="415ee-397">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-397">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="415ee-398">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-398">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="415ee-399"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-399">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="415ee-400">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="415ee-400">Use, Run, and Map</span></span>

<span data-ttu-id="415ee-401"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-401">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="415ee-402">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="415ee-402">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="415ee-403">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-403">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="415ee-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-404"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="415ee-405">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-405">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="415ee-406">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-406">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="415ee-407">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-407">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="415ee-408">요청</span><span class="sxs-lookup"><span data-stu-id="415ee-408">Request</span></span>             | <span data-ttu-id="415ee-409">응답</span><span class="sxs-lookup"><span data-stu-id="415ee-409">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="415ee-410">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="415ee-410">localhost:1234</span></span>      | <span data-ttu-id="415ee-411">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="415ee-411">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="415ee-412">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="415ee-412">localhost:1234/map1</span></span> | <span data-ttu-id="415ee-413">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="415ee-413">Map Test 1</span></span>                   |
| <span data-ttu-id="415ee-414">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="415ee-414">localhost:1234/map2</span></span> | <span data-ttu-id="415ee-415">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="415ee-415">Map Test 2</span></span>                   |
| <span data-ttu-id="415ee-416">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="415ee-416">localhost:1234/map3</span></span> | <span data-ttu-id="415ee-417">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="415ee-417">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="415ee-418">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-418">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="415ee-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-419"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="415ee-420">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-420">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="415ee-421">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-421">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="415ee-422">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-422">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="415ee-423">요청</span><span class="sxs-lookup"><span data-stu-id="415ee-423">Request</span></span>                       | <span data-ttu-id="415ee-424">응답</span><span class="sxs-lookup"><span data-stu-id="415ee-424">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="415ee-425">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="415ee-425">localhost:1234</span></span>                | <span data-ttu-id="415ee-426">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="415ee-426">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="415ee-427">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="415ee-427">localhost:1234/?branch=master</span></span> | <span data-ttu-id="415ee-428">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="415ee-428">Branch used = master</span></span>         |

<span data-ttu-id="415ee-429">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-429">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="415ee-430">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-430">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="415ee-431">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="415ee-431">Built-in middleware</span></span>

<span data-ttu-id="415ee-432">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-432">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="415ee-433">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-433">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="415ee-434">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-434">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware* .</span></span> <span data-ttu-id="415ee-435">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="415ee-435">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="415ee-436">미들웨어</span><span class="sxs-lookup"><span data-stu-id="415ee-436">Middleware</span></span> | <span data-ttu-id="415ee-437">설명</span><span class="sxs-lookup"><span data-stu-id="415ee-437">Description</span></span> | <span data-ttu-id="415ee-438">순서</span><span class="sxs-lookup"><span data-stu-id="415ee-438">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="415ee-439">인증</span><span class="sxs-lookup"><span data-stu-id="415ee-439">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="415ee-440">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-440">Provides authentication support.</span></span> | <span data-ttu-id="415ee-441">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="415ee-441">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="415ee-442">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="415ee-442">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="415ee-443">Cookie 정책</span><span class="sxs-lookup"><span data-stu-id="415ee-443">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="415ee-444">개인 정보 저장과 관련한 사용자의 동의를 추적하고 cookie 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-444">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="415ee-445">cookie를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="415ee-445">Before middleware that issues cookies.</span></span> <span data-ttu-id="415ee-446">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="415ee-446">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="415ee-447">CORS</span><span class="sxs-lookup"><span data-stu-id="415ee-447">CORS</span></span>](xref:security/cors) | <span data-ttu-id="415ee-448">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-448">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="415ee-449">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-449">Before components that use CORS.</span></span> |
| [<span data-ttu-id="415ee-450">진단</span><span class="sxs-lookup"><span data-stu-id="415ee-450">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="415ee-451">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="415ee-451">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="415ee-452">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-452">Before components that generate errors.</span></span> <span data-ttu-id="415ee-453">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-453">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="415ee-454">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="415ee-454">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="415ee-455">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-455">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="415ee-456">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="415ee-456">Before components that consume the updated fields.</span></span> <span data-ttu-id="415ee-457">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="415ee-457">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="415ee-458">상태 검사</span><span class="sxs-lookup"><span data-stu-id="415ee-458">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="415ee-459">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-459">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="415ee-460">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-460">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="415ee-461">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="415ee-461">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="415ee-462">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-462">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="415ee-463">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-463">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="415ee-464">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="415ee-464">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="415ee-465">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-465">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="415ee-466">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-466">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="415ee-467">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="415ee-467">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="415ee-468">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-468">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="415ee-469">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="415ee-469">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="415ee-470">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="415ee-470">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="415ee-471">MVC</span><span class="sxs-lookup"><span data-stu-id="415ee-471">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="415ee-472">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-472">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="415ee-473">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-473">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="415ee-474">OWIN</span><span class="sxs-lookup"><span data-stu-id="415ee-474">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="415ee-475">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-475">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="415ee-476">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-476">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="415ee-477">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="415ee-477">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="415ee-478">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-478">Provides support for caching responses.</span></span> | <span data-ttu-id="415ee-479">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-479">Before components that require caching.</span></span> |
| [<span data-ttu-id="415ee-480">응답 압축</span><span class="sxs-lookup"><span data-stu-id="415ee-480">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="415ee-481">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-481">Provides support for compressing responses.</span></span> | <span data-ttu-id="415ee-482">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-482">Before components that require compression.</span></span> |
| [<span data-ttu-id="415ee-483">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="415ee-483">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="415ee-484">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-484">Provides localization support.</span></span> | <span data-ttu-id="415ee-485">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-485">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="415ee-486">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="415ee-486">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="415ee-487">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-487">Defines and constrains request routes.</span></span> | <span data-ttu-id="415ee-488">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="415ee-488">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="415ee-489">세션</span><span class="sxs-lookup"><span data-stu-id="415ee-489">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="415ee-490">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-490">Provides support for managing user sessions.</span></span> | <span data-ttu-id="415ee-491">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-491">Before components that require Session.</span></span> |
| [<span data-ttu-id="415ee-492">정적 파일</span><span class="sxs-lookup"><span data-stu-id="415ee-492">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="415ee-493">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-493">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="415ee-494">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-494">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="415ee-495">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="415ee-495">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="415ee-496">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-496">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="415ee-497">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-497">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="415ee-498">WebSockets</span><span class="sxs-lookup"><span data-stu-id="415ee-498">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="415ee-499">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="415ee-499">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="415ee-500">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="415ee-500">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="415ee-501">추가 자료</span><span class="sxs-lookup"><span data-stu-id="415ee-501">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
