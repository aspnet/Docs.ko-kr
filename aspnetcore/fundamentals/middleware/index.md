---
title: ASP.NET Core 미들웨어 기본 사항
author: rick-anderson
description: ASP.NET Core 미들웨어 및 요청 파이프라인에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: aa51e53284bc25629b3975ff0e6de967b9a2b866
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513124"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="51215-103">ASP.NET Core 미들웨어</span><span class="sxs-lookup"><span data-stu-id="51215-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51215-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="51215-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="51215-105">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="51215-106">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-106">Each component:</span></span>

* <span data-ttu-id="51215-107">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="51215-108">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="51215-109">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="51215-110">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="51215-111">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="51215-112">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="51215-113">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어* 라고 하며, *미들웨어 구성 요소* 라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="51215-114">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="51215-115">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="51215-116"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="51215-117">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="51215-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="51215-118">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="51215-119">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="51215-120">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="51215-120">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="51215-124">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="51215-125">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="51215-126">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="51215-127">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="51215-128">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="51215-129"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="51215-130">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="51215-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="51215-131">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="51215-132">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="51215-133">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="51215-134">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="51215-135">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어* 로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="51215-136">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="51215-137">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="51215-138">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="51215-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="51215-139">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="51215-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="51215-140">예를 들어 [헤더 및 상태 코드를 설정하면 예외를 throw합니다](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span><span class="sxs-lookup"><span data-stu-id="51215-140">For example, [setting headers and a status code throw an exception](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started).</span></span> <span data-ttu-id="51215-141">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="51215-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="51215-142">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-142">May cause a protocol violation.</span></span> <span data-ttu-id="51215-143">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="51215-144">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-144">May corrupt the body format.</span></span> <span data-ttu-id="51215-145">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="51215-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="51215-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 `next` 매개 변수를 받지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="51215-148">첫 번째 `Run` 대리자는 항상 터미널이며 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="51215-149">`Run`이 규칙입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-149">`Run` is a convention.</span></span> <span data-ttu-id="51215-150">일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="51215-151">위 예제에서 `Run` 대리자는 응답에 `"Hello from 2nd delegate."`를 쓴 다음 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="51215-152">`Run` 대리자 뒤에 추가된 다른 `Use` 또는 `Run` 대리자는 호출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="51215-153">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="51215-153">Middleware order</span></span>

<span data-ttu-id="51215-154">다음 다이어그램은 ASP.NET Core MVC 및 Razor Pages 앱의 전체 요청 처리 파이프라인을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-154">The following diagram shows the complete request processing pipeline for ASP.NET Core MVC and Razor Pages apps.</span></span> <span data-ttu-id="51215-155">일반적인 앱에서 기존 미들웨어의 순서가 지정되는 방식과 사용자 지정 미들웨어가 추가되는 위치를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-155">You can see how, in a typical app, existing middlewares are ordered and where custom middlewares are added.</span></span> <span data-ttu-id="51215-156">사용자는 원하는 대로 기존 미들웨어의 순서를 바꾸거나 시나리오의 필요에 따라 새 사용자 지정 미들웨어를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-156">You have full control over how to reorder existing middlewares or inject new custom middlewares as necessary for your scenarios.</span></span>

![ASP.NET Core 미들웨어 파이프라인](index/_static/middleware-pipeline.svg)

<span data-ttu-id="51215-158">앞에 나온 다이어그램의 **엔드포인트** 미들웨어는 해당 앱 형식&mdash;MVC 또는 Razor Pages에 따라 필터 파이프라인을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-158">The **Endpoint** middleware in the preceding diagram executes the filter pipeline for the corresponding app type&mdash;MVC or Razor Pages.</span></span>

![ASP.NET Core 필터 파이프라인](index/_static/mvc-endpoint.svg)

<span data-ttu-id="51215-160">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-160">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="51215-161">순서는 보안, 성능 및 기능에 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-161">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="51215-162">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 일반적인 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-162">The following `Startup.Configure` method adds security-related middleware components in the typical recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="51215-163">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="51215-163">In the preceding code:</span></span>

* <span data-ttu-id="51215-164">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-164">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="51215-165">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="51215-165">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="51215-166">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="51215-166">For example:</span></span>
  * <span data-ttu-id="51215-167">`UseCors`, `UseAuthentication`, `UseAuthorization`은 표시된 순서를 지켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-167">`UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>
  * <span data-ttu-id="51215-168">현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-168">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>

<span data-ttu-id="51215-169">일부 시나리오의 경우 미들웨어는 다른 순서를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-169">In some scenarios, middleware will have different ordering.</span></span> <span data-ttu-id="51215-170">예를 들어 캐싱 및 압축 순서는 시나리오마다 다르며 유효한 순서가 여러 개 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-170">For example, caching and compression ordering is scenario specific, and there's multiple valid orderings.</span></span> <span data-ttu-id="51215-171">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="51215-171">For example:</span></span>

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

<span data-ttu-id="51215-172">위의 코드를 사용하면 압축된 응답을 캐싱하여 CPU를 저장할 수 있지만 gzip 또는 brotli와 같은 다른 압축 알고리즘을 사용하여 리소스의 여러 표현을 캐싱할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-172">With the preceding code, CPU could be saved by caching the compressed response, but you might end up caching multiple representations of a resource using different compression algorithms such as gzip or brotli.</span></span>

<span data-ttu-id="51215-173">다음 순서는 정적 파일을 결합하여 압축된 정적 파일 캐싱을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-173">The following ordering combines static files to allow caching compressed static files:</span></span>

```csharp
app.UseResponseCaching
app.UseResponseCompression
app.UseStaticFiles
```

<span data-ttu-id="51215-174">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-174">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="51215-175">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="51215-175">Exception/error handling</span></span>
   * <span data-ttu-id="51215-176">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="51215-176">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="51215-177">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-177">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="51215-178">데이터베이스 오류 페이지 미들웨어가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-178">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="51215-179">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="51215-179">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="51215-180">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-180">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="51215-181">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-181">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="51215-182">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-182">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="51215-183">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-183">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="51215-184">Cookie 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="51215-184">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="51215-185">요청을 라우팅하도록 미들웨어(<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) 라우팅.</span><span class="sxs-lookup"><span data-stu-id="51215-185">Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) to route requests.</span></span>
1. <span data-ttu-id="51215-186">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-186">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="51215-187">권한 부여 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>)는 사용자에게 보안 리소스에 액세스할 수 있는 권한을 부여합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-187">Authorization Middleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="51215-188">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-188">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="51215-189">앱이 세션 상태를 사용하는 경우에는 Cookie 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-189">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="51215-190">엔드포인트 라우팅 미들웨어(<xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>를 포함하는 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>)를 사용하여 요청 파이프라인에 Razor Pages 엔드포인트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-190">Endpoint Routing Middleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> with <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="51215-191">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-191">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="51215-192"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-192"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="51215-193">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-193">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="51215-194">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-194">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="51215-195">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="51215-195">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="51215-196">*wwwroot* 에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-196">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="51215-197">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-197">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="51215-198">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-198">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="51215-199">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-199">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="51215-200">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-200">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="51215-201">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-201">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="51215-202">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-202">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="51215-203">Razor Pages 응답을 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-203">The Razor Pages responses can be compressed.</span></span>

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

<span data-ttu-id="51215-204">SPA(단일 페이지 애플리케이션)의 경우 SPA 미들웨어 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A>는 일반적으로 미들웨어 파이프라인에서 마지막으로 나옵니다.</span><span class="sxs-lookup"><span data-stu-id="51215-204">For Single Page Applications (SPAs), the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="51215-205">SPA 미들웨어가 마지막으로 나오는 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-205">The SPA middleware comes last:</span></span>

* <span data-ttu-id="51215-206">다른 모든 미들웨어가 일치하는 요청에 먼저 응답하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-206">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="51215-207">클라이언트 쪽 라우팅이 있는 SPA가 서버 앱에서 인식할 수 없는 모든 경로에 대해 실행되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-207">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="51215-208">SPA에 대한 자세한 내용은 [React](xref:spa/react) 및 [Angular](xref:spa/angular) 프로젝트 템플릿 관련 가이드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-208">For more details on SPAs, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="51215-209">전달된 헤더 미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="51215-209">Forwarded Headers Middleware order</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="51215-210">미들웨어 파이프라인 분기</span><span class="sxs-lookup"><span data-stu-id="51215-210">Branch the middleware pipeline</span></span>

<span data-ttu-id="51215-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-211"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="51215-212">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-212">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="51215-213">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-213">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="51215-214">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-214">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="51215-215">요청</span><span class="sxs-lookup"><span data-stu-id="51215-215">Request</span></span>             | <span data-ttu-id="51215-216">응답</span><span class="sxs-lookup"><span data-stu-id="51215-216">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="51215-217">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="51215-217">localhost:1234</span></span>      | <span data-ttu-id="51215-218">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="51215-218">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="51215-219">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="51215-219">localhost:1234/map1</span></span> | <span data-ttu-id="51215-220">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="51215-220">Map Test 1</span></span>                   |
| <span data-ttu-id="51215-221">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="51215-221">localhost:1234/map2</span></span> | <span data-ttu-id="51215-222">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="51215-222">Map Test 2</span></span>                   |
| <span data-ttu-id="51215-223">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="51215-223">localhost:1234/map3</span></span> | <span data-ttu-id="51215-224">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="51215-224">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="51215-225">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-225">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="51215-226">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-226">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="51215-227">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-227">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="51215-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-228"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="51215-229">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-229">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="51215-230">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-230">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="51215-231">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-231">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="51215-232">요청</span><span class="sxs-lookup"><span data-stu-id="51215-232">Request</span></span>                       | <span data-ttu-id="51215-233">응답</span><span class="sxs-lookup"><span data-stu-id="51215-233">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="51215-234">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="51215-234">localhost:1234</span></span>                | <span data-ttu-id="51215-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="51215-235">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="51215-236">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="51215-236">localhost:1234/?branch=master</span></span> | <span data-ttu-id="51215-237">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="51215-237">Branch used = master</span></span>         |

<span data-ttu-id="51215-238"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A>도 지정된 조건자의 결과를 기준으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-238"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="51215-239">`MapWhen`과 달리, 이 분기는 단락을 수행하거나 터미널 미들웨어를 포함하지 않는 경우 기본 파이프라인에 다시 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-239">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it doesn't short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

<span data-ttu-id="51215-240">위 예제에서는 “Hello from main pipeline.” 응답이</span><span class="sxs-lookup"><span data-stu-id="51215-240">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="51215-241">모든 요청에 대해 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-241">is written for all requests.</span></span> <span data-ttu-id="51215-242">요청에 쿼리 문자열 변수 `branch`가 포함되어 있으면, 기본 파이프라인이 다시 연결되기 전에 변수 값이 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-242">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="51215-243">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="51215-243">Built-in middleware</span></span>

<span data-ttu-id="51215-244">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-244">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="51215-245">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-245">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="51215-246">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-246">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="51215-247">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-247">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="51215-248">미들웨어</span><span class="sxs-lookup"><span data-stu-id="51215-248">Middleware</span></span> | <span data-ttu-id="51215-249">설명</span><span class="sxs-lookup"><span data-stu-id="51215-249">Description</span></span> | <span data-ttu-id="51215-250">순서</span><span class="sxs-lookup"><span data-stu-id="51215-250">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="51215-251">인증</span><span class="sxs-lookup"><span data-stu-id="51215-251">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="51215-252">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-252">Provides authentication support.</span></span> | <span data-ttu-id="51215-253">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="51215-253">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="51215-254">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="51215-254">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="51215-255">권한 부여</span><span class="sxs-lookup"><span data-stu-id="51215-255">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | <span data-ttu-id="51215-256">권한 부여 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-256">Provides authorization support.</span></span> | <span data-ttu-id="51215-257">인증 미들웨어 바로 뒤에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-257">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="51215-258">Cookie 정책</span><span class="sxs-lookup"><span data-stu-id="51215-258">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="51215-259">개인 정보 저장과 관련한 사용자의 동의를 추적하고 cookie 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-259">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="51215-260">cookie를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="51215-260">Before middleware that issues cookies.</span></span> <span data-ttu-id="51215-261">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="51215-261">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="51215-262">CORS</span><span class="sxs-lookup"><span data-stu-id="51215-262">CORS</span></span>](xref:security/cors) | <span data-ttu-id="51215-263">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-263">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="51215-264">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-264">Before components that use CORS.</span></span> <span data-ttu-id="51215-265">현재 `UseCors`는 [이 버그](https://github.com/dotnet/aspnetcore/issues/23218)로 인해 `UseResponseCaching`보다 먼저 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-265">`UseCors` currently must go before `UseResponseCaching` due to [this bug](https://github.com/dotnet/aspnetcore/issues/23218).</span></span>|
| [<span data-ttu-id="51215-266">진단</span><span class="sxs-lookup"><span data-stu-id="51215-266">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="51215-267">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="51215-267">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="51215-268">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-268">Before components that generate errors.</span></span> <span data-ttu-id="51215-269">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-269">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="51215-270">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="51215-270">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="51215-271">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-271">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="51215-272">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="51215-272">Before components that consume the updated fields.</span></span> <span data-ttu-id="51215-273">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="51215-273">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="51215-274">상태 검사</span><span class="sxs-lookup"><span data-stu-id="51215-274">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="51215-275">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-275">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="51215-276">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-276">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="51215-277">헤더 전파</span><span class="sxs-lookup"><span data-stu-id="51215-277">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="51215-278">들어오는 요청에서 나가는 HTTP 클라이언트 요청으로 HTTP 헤더를 전파합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-278">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="51215-279">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="51215-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="51215-280">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="51215-281">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="51215-282">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="51215-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="51215-283">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="51215-284">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="51215-285">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="51215-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="51215-286">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="51215-287">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="51215-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="51215-288">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="51215-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="51215-289">MVC</span><span class="sxs-lookup"><span data-stu-id="51215-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="51215-290">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="51215-291">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="51215-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="51215-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="51215-293">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="51215-294">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="51215-295">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="51215-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="51215-296">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-296">Provides support for caching responses.</span></span> | <span data-ttu-id="51215-297">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-297">Before components that require caching.</span></span> <span data-ttu-id="51215-298">`UseCORS`는 `UseResponseCaching` 앞에 와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-298">`UseCORS` must come before `UseResponseCaching`.</span></span>|
| [<span data-ttu-id="51215-299">응답 압축</span><span class="sxs-lookup"><span data-stu-id="51215-299">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="51215-300">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-300">Provides support for compressing responses.</span></span> | <span data-ttu-id="51215-301">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-301">Before components that require compression.</span></span> |
| [<span data-ttu-id="51215-302">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="51215-302">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="51215-303">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-303">Provides localization support.</span></span> | <span data-ttu-id="51215-304">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-304">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="51215-305">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="51215-305">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="51215-306">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-306">Defines and constrains request routes.</span></span> | <span data-ttu-id="51215-307">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="51215-307">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="51215-308">SPA</span><span class="sxs-lookup"><span data-stu-id="51215-308">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | <span data-ttu-id="51215-309">SPA(단일 페이지 애플리케이션)의 기반 페이지를 반환하여 이 시점부터 미들웨어 체인의 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-309">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="51215-310">정적 파일, MVC 작업 등을 처리하는 다른 미들웨어가 우선할 수 있도록 이 미들웨어는 체인에서 늦게 배치되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-310">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="51215-311">세션</span><span class="sxs-lookup"><span data-stu-id="51215-311">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="51215-312">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-312">Provides support for managing user sessions.</span></span> | <span data-ttu-id="51215-313">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-313">Before components that require Session.</span></span> | 
| [<span data-ttu-id="51215-314">정적 파일</span><span class="sxs-lookup"><span data-stu-id="51215-314">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="51215-315">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-315">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="51215-316">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-316">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="51215-317">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="51215-317">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="51215-318">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-318">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="51215-319">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-319">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="51215-320">WebSockets</span><span class="sxs-lookup"><span data-stu-id="51215-320">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="51215-321">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-321">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="51215-322">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-322">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="51215-323">추가 자료</span><span class="sxs-lookup"><span data-stu-id="51215-323">Additional resources</span></span>

* <span data-ttu-id="51215-324">[수명 및 등록 옵션](xref:fundamentals/dependency-injection#lifetime-and-registration-options) ‘범위가 지정되고’, ‘일시적인’ *singleton* 수명 서비스를 사용하는 미들웨어의 전체 샘플이 포함되어 있습니다. </span><span class="sxs-lookup"><span data-stu-id="51215-324">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped*, *transient*, and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51215-325">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="51215-325">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="51215-326">미들웨어는 요청 및 응답을 처리하는 앱 파이프라인으로 조립되는 소프트웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-326">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="51215-327">각 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-327">Each component:</span></span>

* <span data-ttu-id="51215-328">요청을 파이프라인의 다음 구성 요소로 전달할지 여부를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-328">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="51215-329">파이프라인의 다음 구성 요소 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-329">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="51215-330">요청 대리자는 요청 파이프라인을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-330">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="51215-331">요청 대리자는 각 HTTP 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-331">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="51215-332">요청 대리자는 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 및 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> 확장 메서드를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-332">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> extension methods.</span></span> <span data-ttu-id="51215-333">개별 요청 대리자는 무명 메서드(인라인 미들웨어라고 함)로 인라인에서 지정되거나 다시 사용할 수 있는 클래스에서 정의될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-333">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="51215-334">이러한 다시 사용할 수 있는 클래스 및 인라인 무명 메서드를 *미들웨어* 라고 하며, *미들웨어 구성 요소* 라고 부르기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-334">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="51215-335">요청 파이프라인의 각 미들웨어 구성 요소는 파이프라인의 그 다음 구성 요소를 호출하거나 파이프라인을 단락(short-circuiting)하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-335">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="51215-336">미들웨어가 단락(short-circuit)되는 경우 미들웨어에서 더는 요청을 처리하지 못하도록 하기 때문에 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-336">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="51215-337"><xref:migration/http-modules>은 ASP.NET Core와 ASP.NET 4.x의 요청 파이프라인 간의 차이점을 설명하고 추가 미들웨어 샘플을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-337"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="51215-338">IApplicationBuilder로 미들웨어 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="51215-338">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="51215-339">ASP.NET Core 요청 파이프라인은 하나씩 차례로 호출되는 요청 대리자 시퀀스로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-339">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="51215-340">다음 다이어그램은 그 개념을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-340">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="51215-341">실행 스레드는 검은색 화살표를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="51215-341">The thread of execution follows the black arrows.</span></span>

![요청이 도착하고, 세 가지 미들웨어에 의해서 처리되고, 앱에서 응답이 나가는 것을 보여주는 요청 처리 패턴입니다.](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="51215-345">각 대리자는 다음 대리자 전과 후에 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-345">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="51215-346">예외 처리 대리자는 파이프라인의 이후 단계에서 발생하는 예외를 잡을 수 있도록 파이프라인의 초기에 호출되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-346">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="51215-347">가장 간단한 가능한 ASP.NET Core 앱은 모든 요청을 처리하는 단일 요청 대리자를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-347">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="51215-348">이 경우 실제 요청 파이프라인은 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-348">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="51215-349">대신, 단일 익명 함수가 모든 HTTP 요청에 대한 응답에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-349">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="51215-350">첫 번째 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 대리자는 파이프라인을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-350">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> delegate terminates the pipeline.</span></span>

<span data-ttu-id="51215-351"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>를 사용하여 여러 요청 대리자를 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-351">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>.</span></span> <span data-ttu-id="51215-352">`next` 매개 변수는 파이프라인의 다음 대리자를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="51215-352">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="51215-353">*next* 매개 변수를 호출하지 *않고* 파이프라인을 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-353">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="51215-354">다음 예제의 설명처럼, 일반적으로 다음 대리자 전과 후 모두에서 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-354">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="51215-355">대리자가 다음 대리자에 요청을 전달하지 않을 때 이를 *요청 파이프라인을 단락(short-circuiting)* 한다고 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-355">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="51215-356">단락(short-circuiting)은 불필요한 작업을 방지하기 때문에 종종 바람직합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-356">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="51215-357">예를 들어 [정적 파일 미들웨어](xref:fundamentals/static-files)는 정적 파일에 대한 요청을 처리하고 나머지 파이프라인을 단락(short-circuit)하여 *터미널 미들웨어* 로 작동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-357">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="51215-358">추가 처리를 종료하는 미들웨어 전에 파이프라인에 추가된 미들웨어는 `next.Invoke` 문 이후의 코드를 계속 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-358">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="51215-359">그러나 이미 전송된 응답에 쓰려고 시도하는 것에 대한 다음 경고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-359">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="51215-360">클라이언트에 응답을 전송한 후에 `next.Invoke`를 호출하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="51215-360">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="51215-361">응답이 시작된 후 <xref:Microsoft.AspNetCore.Http.HttpResponse>로 변경하면 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="51215-361">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="51215-362">예를 들어 헤더 및 상태 코드를 설정하는 변경 작업은 예외를 던집니다.</span><span class="sxs-lookup"><span data-stu-id="51215-362">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="51215-363">`next`를 호출한 후 응답 본문에 작성할 경우:</span><span class="sxs-lookup"><span data-stu-id="51215-363">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="51215-364">프로토콜 위반이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-364">May cause a protocol violation.</span></span> <span data-ttu-id="51215-365">예를 들어, 명시된 `Content-Length`보다 긴 내용이 작성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-365">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="51215-366">본문 형식을 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-366">May corrupt the body format.</span></span> <span data-ttu-id="51215-367">예를 들어, CSS 파일에 HTML 바닥글을 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-367">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="51215-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A>는 헤더가 이미 전송됐는지 또는 본문이 이미 작성됐는지 여부를 나타내는 유용한 힌트를 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-368"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="51215-369">미들웨어 순서</span><span class="sxs-lookup"><span data-stu-id="51215-369">Middleware order</span></span>

<span data-ttu-id="51215-370">미들웨어 구성 요소가 `Startup.Configure` 메서드에 추가되는 순서는 요청에서 미들웨어 구성 요소가 호출되는 순서와 응답에 대한 역순서를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-370">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="51215-371">순서는 보안, 성능 및 기능에 **중요** 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-371">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="51215-372">다음 `Startup.Configure` 메서드는 보안 관련 미들웨어 구성 요소를 권장 순서에 따라 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-372">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="51215-373">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="51215-373">In the preceding code:</span></span>

* <span data-ttu-id="51215-374">[개별 사용자 계정](xref:security/authentication/identity)을 사용하여 새 웹앱을 만들 때 추가되지 않는 미들웨어는 주석 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-374">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="51215-375">모든 미들웨어가 정확한 순서대로 이동해야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="51215-375">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="51215-376">예를 들어 `UseCors` 및 `UseAuthentication`은 표시된 순서대로 이동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-376">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="51215-377">다음 `Startup.Configure` 메서드는 일반적인 앱 시나리오를 위한 미들웨어 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-377">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="51215-378">예외/오류 처리</span><span class="sxs-lookup"><span data-stu-id="51215-378">Exception/error handling</span></span>
   * <span data-ttu-id="51215-379">앱이 개발 환경에서 실행되는 경우:</span><span class="sxs-lookup"><span data-stu-id="51215-379">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="51215-380">개발자 예외 페이지 미들웨어(<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>)가 앱 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-380">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) reports app runtime errors.</span></span>
     * <span data-ttu-id="51215-381">데이터베이스 오류 페이지 미들웨어(`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`)가 데이터베이스 런타임 오류를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-381">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="51215-382">프로덕션 환경에서 앱을 실행하는 경우:</span><span class="sxs-lookup"><span data-stu-id="51215-382">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="51215-383">예외 처리기 미들웨어(<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>)가 다음 미들웨어에서 던져진 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-383">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="51215-384">HTTP HSTS(엄격한 전송 보안 프로토콜) 미들웨어(<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>)가 `Strict-Transport-Security` 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-384">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="51215-385">HTTPS 리디렉션 미들웨어(<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>)가 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-385">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="51215-386">정적 파일 미들웨어(<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>)가 정적 파일을 반환하고 추가 요청 처리를 단락합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-386">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="51215-387">Cookie 정책 미들웨어(<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>)가 앱이 EU GDPR(일반 데이터 보호 규정)을 준수하도록 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="51215-387">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="51215-388">인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)가 보안 리소스에 대한 액세스가 허용되기 전에 사용자 인증을 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-388">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="51215-389">세션 미들웨어(<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>)가 세션 상태를 설정 및 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-389">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) establishes and maintains session state.</span></span> <span data-ttu-id="51215-390">앱이 세션 상태를 사용하는 경우에는 Cookie 정책 미들웨어 이후 및 MVC 미들웨어 이전에 세션 미들웨어를 호출하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-390">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="51215-391">MVC(<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>)는 MVC를 요청 파이프라인에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-391">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="51215-392">앞의 예제 코드에서 각 미들웨어 확장 메서드는 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 네임스페이스를 통해 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-392">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="51215-393"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>는 파이프라인에 처음으로 추가된 미들웨어 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-393"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="51215-394">따라서 예외 처리기 미들웨어는 후속 호출에서 발생하는 모든 예외를 잡습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-394">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="51215-395">정적 파일 미들웨어는 파이프라인 초기에 호출되므로 요청을 처리하고 나머지 구성 요소를 통과하지 않고 단락(short-circuit)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-395">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="51215-396">정적 파일 미들웨어는 권한 부여 검사를 제공하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="51215-396">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="51215-397">*wwwroot* 에 있는 파일을 포함한 정적 파일 미들웨어에서 제공하는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-397">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="51215-398">정적 파일을 보호하는 방법은 <xref:fundamentals/static-files>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-398">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="51215-399">요청이 정적 파일 미들웨어에서 처리되지 않는 경우 인증을 수행하는 인증 미들웨어(<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-399">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>), which performs authentication.</span></span> <span data-ttu-id="51215-400">인증은 인증되지 않은 요청을 단락(short-circuit)하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-400">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="51215-401">인증 미들웨어가 요청을 인증하지만, MVC가 특정 Razor Page 또는 컨트롤러 및 작업을 선택한 후에만 권한 부여(및 거부)가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-401">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="51215-402">다음 예제는 정적 파일에 대한 요청이 응답 압축 미들웨어 전에 정적 파일 미들웨어에서 처리되는 미들웨어 순서를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-402">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="51215-403">정적 파일은 이 미들웨어 순서를 사용하여 압축되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-403">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="51215-404"><xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A>의 MVC 응답은 압축할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-404">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="51215-405">Use, Run 및 Map</span><span class="sxs-lookup"><span data-stu-id="51215-405">Use, Run, and Map</span></span>

<span data-ttu-id="51215-406"><xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> 및 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>을 사용하여 HTTP 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-406">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>.</span></span> <span data-ttu-id="51215-407">`Use` 메서드는 파이프라인을 단락(short-circuit)할 수 있습니다(즉, `next` 요청 대리자를 호출하지 않는 경우).</span><span class="sxs-lookup"><span data-stu-id="51215-407">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="51215-408">`Run`은 규칙이며 일부 미들웨어 구성 요소는 파이프라인의 끝에서 실행되는 `Run[Middleware]` 메서드를 노출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-408">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="51215-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> 확장은 파이프라인 분기에 규칙으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-409"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="51215-410">`Map`은 지정된 요청 경로의 일치를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-410">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="51215-411">요청 경로가 지정된 경로로 시작하는 경우 분기가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-411">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="51215-412">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-412">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="51215-413">요청</span><span class="sxs-lookup"><span data-stu-id="51215-413">Request</span></span>             | <span data-ttu-id="51215-414">응답</span><span class="sxs-lookup"><span data-stu-id="51215-414">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="51215-415">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="51215-415">localhost:1234</span></span>      | <span data-ttu-id="51215-416">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="51215-416">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="51215-417">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="51215-417">localhost:1234/map1</span></span> | <span data-ttu-id="51215-418">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="51215-418">Map Test 1</span></span>                   |
| <span data-ttu-id="51215-419">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="51215-419">localhost:1234/map2</span></span> | <span data-ttu-id="51215-420">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="51215-420">Map Test 2</span></span>                   |
| <span data-ttu-id="51215-421">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="51215-421">localhost:1234/map3</span></span> | <span data-ttu-id="51215-422">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="51215-422">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="51215-423">`Map`이 사용되는 경우 일치하는 경로 세그먼트는 `HttpRequest.Path`에서 제거되고 각 요청에 대해 `HttpRequest.PathBase`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-423">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="51215-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A>은 지정된 조건자의 결과를 기반으로 요청 파이프라인을 분기합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-424"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="51215-425">`Func<HttpContext, bool>` 형식의 조건자는 파이프라인의 새 분기에 요청을 매핑하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-425">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="51215-426">다음 예제에서 조건자는 쿼리 문자열 변수 `branch`의 존재를 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-426">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="51215-427">다음 표는 앞의 코드를 사용하여 `http://localhost:1234`의 요청 및 응답을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="51215-427">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="51215-428">요청</span><span class="sxs-lookup"><span data-stu-id="51215-428">Request</span></span>                       | <span data-ttu-id="51215-429">응답</span><span class="sxs-lookup"><span data-stu-id="51215-429">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="51215-430">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="51215-430">localhost:1234</span></span>                | <span data-ttu-id="51215-431">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="51215-431">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="51215-432">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="51215-432">localhost:1234/?branch=master</span></span> | <span data-ttu-id="51215-433">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="51215-433">Branch used = master</span></span>         |

<span data-ttu-id="51215-434">`Map`은 중첩을 지원합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-434">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="51215-435">`Map`은 여러 세그먼트를 한 번에 일치시킬 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51215-435">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="51215-436">기본 제공 미들웨어</span><span class="sxs-lookup"><span data-stu-id="51215-436">Built-in middleware</span></span>

<span data-ttu-id="51215-437">ASP.NET Core는 다음과 같은 미들웨어 구성 요소와 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-437">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="51215-438">*순서* 열은 요청 처리 파이프라인에서 미들웨어의 배치, 미들웨어가 요청 처리를 종료할 수 있는 조건에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-438">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="51215-439">미들웨어가 요청 처리 파이프라인을 단락(short-circuit)하고 후속 미들웨어가 더는 요청을 처리하지 못하도록 하는 경우 이를 *터미널 미들웨어* 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-439">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="51215-440">단락(short-circuiting)에 대한 자세한 내용은 [IApplicationBuilder로 미들웨어 파이프라인 만들기](#create-a-middleware-pipeline-with-iapplicationbuilder) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51215-440">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="51215-441">미들웨어</span><span class="sxs-lookup"><span data-stu-id="51215-441">Middleware</span></span> | <span data-ttu-id="51215-442">설명</span><span class="sxs-lookup"><span data-stu-id="51215-442">Description</span></span> | <span data-ttu-id="51215-443">순서</span><span class="sxs-lookup"><span data-stu-id="51215-443">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="51215-444">인증</span><span class="sxs-lookup"><span data-stu-id="51215-444">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="51215-445">인증 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-445">Provides authentication support.</span></span> | <span data-ttu-id="51215-446">`HttpContext.User`가 필요하기 전에.</span><span class="sxs-lookup"><span data-stu-id="51215-446">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="51215-447">OAuth 콜백에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="51215-447">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="51215-448">Cookie 정책</span><span class="sxs-lookup"><span data-stu-id="51215-448">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="51215-449">개인 정보 저장과 관련한 사용자의 동의를 추적하고 cookie 필드(예: `secure` 및 `SameSite`)에 대해 최소한의 표준을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-449">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="51215-450">cookie를 발행하는 미들웨어 전에.</span><span class="sxs-lookup"><span data-stu-id="51215-450">Before middleware that issues cookies.</span></span> <span data-ttu-id="51215-451">예: 인증, 세션, MVC(TempData).</span><span class="sxs-lookup"><span data-stu-id="51215-451">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="51215-452">CORS</span><span class="sxs-lookup"><span data-stu-id="51215-452">CORS</span></span>](xref:security/cors) | <span data-ttu-id="51215-453">원본 간 리소스 공유를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-453">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="51215-454">CORS를 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-454">Before components that use CORS.</span></span> |
| [<span data-ttu-id="51215-455">진단</span><span class="sxs-lookup"><span data-stu-id="51215-455">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="51215-456">개발자 예외 페이지, 예외 처리, 상태 코드 페이지 및 새 앱에 대한 기본 웹 페이지를 제공하는 몇 가지 개별 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="51215-456">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="51215-457">오류를 생성하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-457">Before components that generate errors.</span></span> <span data-ttu-id="51215-458">예외가 발생하거나 새 앱의 기본 웹 페이지를 처리하는 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-458">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="51215-459">전달된 헤더</span><span class="sxs-lookup"><span data-stu-id="51215-459">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="51215-460">프록시된 헤더를 현재 요청에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-460">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="51215-461">업데이트된 필드를 사용하는 구성 요소 전에.</span><span class="sxs-lookup"><span data-stu-id="51215-461">Before components that consume the updated fields.</span></span> <span data-ttu-id="51215-462">예: 체계, 호스트, 클라이언트 IP, 메서드.</span><span class="sxs-lookup"><span data-stu-id="51215-462">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="51215-463">상태 검사</span><span class="sxs-lookup"><span data-stu-id="51215-463">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="51215-464">ASP.NET Core 앱 및 그 종속성(데이터베이스 가용성 등)의 상태를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-464">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="51215-465">요청이 상태 검사 엔드포인트와 일치하는 경우 마지막입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-465">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="51215-466">HTTP 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="51215-466">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="51215-467">들어오는 POST 요청이 메서드를 재정의하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-467">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="51215-468">업데이트된 메서드를 사용하는 구성 요소 앞입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-468">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="51215-469">HTTPS 리디렉션</span><span class="sxs-lookup"><span data-stu-id="51215-469">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="51215-470">모든 HTTP 요청을 HTTPS로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-470">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="51215-471">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-471">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="51215-472">HSTS(HTTP 엄격한 전송 보안)</span><span class="sxs-lookup"><span data-stu-id="51215-472">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="51215-473">특별한 응답 헤더를 추가하는 보안 향상 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-473">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="51215-474">응답이 전송되기 이전, 요청을 수정하는 구성 요소 이후에.</span><span class="sxs-lookup"><span data-stu-id="51215-474">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="51215-475">예: 전달된 헤더, URL 재작성.</span><span class="sxs-lookup"><span data-stu-id="51215-475">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="51215-476">MVC</span><span class="sxs-lookup"><span data-stu-id="51215-476">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="51215-477">MVC/Razor Pages를 사용하여 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-477">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="51215-478">요청이 경로와 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-478">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="51215-479">OWIN</span><span class="sxs-lookup"><span data-stu-id="51215-479">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="51215-480">OWIN 기반 앱, 서버 및 미들웨어와 상호 운용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51215-480">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="51215-481">OWIN 미들웨어가 요청을 완벽하게 처리하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-481">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="51215-482">응답 캐싱</span><span class="sxs-lookup"><span data-stu-id="51215-482">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="51215-483">응답 캐시에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-483">Provides support for caching responses.</span></span> | <span data-ttu-id="51215-484">캐싱이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-484">Before components that require caching.</span></span> |
| [<span data-ttu-id="51215-485">응답 압축</span><span class="sxs-lookup"><span data-stu-id="51215-485">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="51215-486">응답 압축에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-486">Provides support for compressing responses.</span></span> | <span data-ttu-id="51215-487">압축이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-487">Before components that require compression.</span></span> |
| [<span data-ttu-id="51215-488">요청 지역화</span><span class="sxs-lookup"><span data-stu-id="51215-488">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="51215-489">지역화 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-489">Provides localization support.</span></span> | <span data-ttu-id="51215-490">지역화 구분 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-490">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="51215-491">엔드포인트 라우팅</span><span class="sxs-lookup"><span data-stu-id="51215-491">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="51215-492">요청 경로를 정의하고 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-492">Defines and constrains request routes.</span></span> | <span data-ttu-id="51215-493">경로 일치에 대한 터미널.</span><span class="sxs-lookup"><span data-stu-id="51215-493">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="51215-494">세션</span><span class="sxs-lookup"><span data-stu-id="51215-494">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="51215-495">사용자 세션 관리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-495">Provides support for managing user sessions.</span></span> | <span data-ttu-id="51215-496">세션이 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-496">Before components that require Session.</span></span> |
| [<span data-ttu-id="51215-497">정적 파일</span><span class="sxs-lookup"><span data-stu-id="51215-497">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="51215-498">정적 파일 및 디렉터리 검색 처리에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-498">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="51215-499">요청이 파일과 일치하는 경우 터미널입니다.</span><span class="sxs-lookup"><span data-stu-id="51215-499">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="51215-500">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="51215-500">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="51215-501">URL 재작성 및 요청 리디렉션에 대한 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-501">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="51215-502">URL을 사용하는 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-502">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="51215-503">WebSockets</span><span class="sxs-lookup"><span data-stu-id="51215-503">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="51215-504">WebSocket 프로토콜을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="51215-504">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="51215-505">WebSocket 요청을 수락하는 데 필요한 구성 요소 이전.</span><span class="sxs-lookup"><span data-stu-id="51215-505">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="51215-506">추가 자료</span><span class="sxs-lookup"><span data-stu-id="51215-506">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
