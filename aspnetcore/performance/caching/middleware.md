---
title: ASP.NET Core의 응답 캐싱 미들웨어
author: rick-anderson
description: ASP.NET Core에서 응답 캐싱 미들웨어를 구성하고 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 0f1f5dfcb9595270a9659a02141f7d1eba5c44ef
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587699"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="d4e84-103">ASP.NET Core의 응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="d4e84-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="d4e84-104">작성자: [John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="d4e84-104">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4e84-105">이 문서에서는 ASP.NET Core 앱에서 응답 캐싱 미들웨어를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="d4e84-106">미들웨어는 응답을 캐시할 시기를 결정 하 고, 응답을 저장 하 고, 캐시에서 응답을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="d4e84-107">HTTP 캐싱 및 특성에 대 한 소개는 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) [응답 캐싱](xref:performance/caching/response)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="d4e84-108">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4e84-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="d4e84-109">구성</span><span class="sxs-lookup"><span data-stu-id="d4e84-109">Configuration</span></span>

<span data-ttu-id="d4e84-110">응답 캐싱 미들웨어는 공유 프레임 워크를 통해 ASP.NET Core 앱에 대해 암시적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="d4e84-111">에서 `Startup.ConfigureServices` 응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="d4e84-112"><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>의 요청 처리 파이프라인에 미들웨어를 추가 하는 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <span data-ttu-id="d4e84-113"><xref:Owin.CorsExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> [CORS 미들웨어](xref:security/cors)를 사용 하는 경우 먼저를 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-113"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using [CORS middleware](xref:security/cors).</span></span>

<span data-ttu-id="d4e84-114">샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-114">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="d4e84-115">[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2): 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-115">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="d4e84-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): 후속 요청의 [수락 인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청과 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="d4e84-117">위의 헤더는 응답에 기록 되지 않으며 컨트롤러, 작업 또는 페이지에 대해 재정의 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="d4e84-117">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="d4e84-118">에는 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-118">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="d4e84-119">속성이 설정 되지 않은 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-119">This applies even if a property isn't set.</span></span> <span data-ttu-id="d4e84-120">예를 들어 [Varybyheader](./response.md#vary) 속성을 생략 하면 해당 헤더가 응답에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-120">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="d4e84-121">응답 캐싱 미들웨어는 200 (OK) 상태 코드를 발생 시키는 서버 응답만 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-121">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="d4e84-122">[오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-122">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="d4e84-123">인증 된 클라이언트에 대 한 콘텐츠를 포함 하는 응답은 미들웨어가 해당 응답을 저장 및 제공 하지 못하도록 캐시할 수 없음으로 표시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-123">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="d4e84-124">미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-124">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="d4e84-125">옵션</span><span class="sxs-lookup"><span data-stu-id="d4e84-125">Options</span></span>

<span data-ttu-id="d4e84-126">응답 캐싱 옵션은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-126">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="d4e84-127">옵션</span><span class="sxs-lookup"><span data-stu-id="d4e84-127">Option</span></span> | <span data-ttu-id="d4e84-128">설명</span><span class="sxs-lookup"><span data-stu-id="d4e84-128">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="d4e84-129">응답 본문에 대해 캐시할 수 있는 최대 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-129">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="d4e84-130">기본값은 `64 * 1024 * 1024` (64 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-130">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="d4e84-131">응답 캐시 미들웨어의 크기 제한 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-131">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="d4e84-132">기본값은 `100 * 1024 * 1024` (100 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-132">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="d4e84-133">대/소문자를 구분 하는 경로에서 응답이 캐시 되는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-133">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="d4e84-134">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-134">The default value is `false`.</span></span> |

<span data-ttu-id="d4e84-135">다음 예제에서는 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-135">The following example configures the middleware to:</span></span>

* <span data-ttu-id="d4e84-136">본문 크기가 1024 바이트 보다 작거나 같은 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-136">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="d4e84-137">대/소문자 구분 경로를 기준으로 응답을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-137">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="d4e84-138">예를 들어 `/page1` 및 `/Page1` 은 별도로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-138">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="d4e84-139">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="d4e84-139">VaryByQueryKeys</span></span>

<span data-ttu-id="d4e84-140">MVC/web API 컨트롤러 또는 Razor 페이지 페이지 모델을 사용 하는 경우 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성은 응답 캐싱에 적합 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-140">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="d4e84-141">`[ResponseCache]`미들웨어를 엄격히 필요로 하는 특성의 유일한 매개 변수는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> 실제 HTTP 헤더에 해당 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-141">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="d4e84-142">자세한 내용은 <xref:performance/caching/response#responsecache-attribute>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-142">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="d4e84-143">특성을 사용 하지 않는 경우 `[ResponseCache]` 응답 캐싱은에서 달라질 수 있습니다 `VaryByQueryKeys` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-143">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="d4e84-144"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>다음과 같이 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)에서 직접를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-144">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="d4e84-145">에서와 같은 단일 값을 사용 하는 경우 `*` `VaryByQueryKeys` 모든 요청 쿼리 매개 변수에 의해 캐시가 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-145">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="d4e84-146">응답 캐싱 미들웨어에서 사용 하는 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="d4e84-146">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="d4e84-147">다음 표에서는 응답 캐싱에 영향을 주는 HTTP 헤더에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-147">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="d4e84-148">header</span><span class="sxs-lookup"><span data-stu-id="d4e84-148">Header</span></span> | <span data-ttu-id="d4e84-149">세부 정보</span><span class="sxs-lookup"><span data-stu-id="d4e84-149">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="d4e84-150">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-150">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="d4e84-151">미들웨어는 cache 지시문으로 표시 된 캐싱 응답만 고려 합니다 `public` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-151">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="d4e84-152">다음 매개 변수를 사용 하 여 캐싱 제어:</span><span class="sxs-lookup"><span data-stu-id="d4e84-152">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="d4e84-153">최대 사용 기간</span><span class="sxs-lookup"><span data-stu-id="d4e84-153">max-age</span></span></li><li><span data-ttu-id="d4e84-154">최대-오래 된&#8224;</span><span class="sxs-lookup"><span data-stu-id="d4e84-154">max-stale&#8224;</span></span></li><li><span data-ttu-id="d4e84-155">최소-새로</span><span class="sxs-lookup"><span data-stu-id="d4e84-155">min-fresh</span></span></li><li><span data-ttu-id="d4e84-156">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="d4e84-156">must-revalidate</span></span></li><li><span data-ttu-id="d4e84-157">no-cache</span><span class="sxs-lookup"><span data-stu-id="d4e84-157">no-cache</span></span></li><li><span data-ttu-id="d4e84-158">저장소 없음</span><span class="sxs-lookup"><span data-stu-id="d4e84-158">no-store</span></span></li><li><span data-ttu-id="d4e84-159">-인 경우에만 캐시</span><span class="sxs-lookup"><span data-stu-id="d4e84-159">only-if-cached</span></span></li><li><span data-ttu-id="d4e84-160">private</span><span class="sxs-lookup"><span data-stu-id="d4e84-160">private</span></span></li><li><span data-ttu-id="d4e84-161">public</span><span class="sxs-lookup"><span data-stu-id="d4e84-161">public</span></span></li><li><span data-ttu-id="d4e84-162">s-maxage</span><span class="sxs-lookup"><span data-stu-id="d4e84-162">s-maxage</span></span></li><li><span data-ttu-id="d4e84-163">프록시-유효성 검사&#8225;</span><span class="sxs-lookup"><span data-stu-id="d4e84-163">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="d4e84-164">&#8224;에 대 한 제한이 지정 되지 않은 경우 `max-stale` 미들웨어는 아무 작업도 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-164">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="d4e84-165">&#8225;는 `proxy-revalidate` 와 동일한 효과가 있습니다 `must-revalidate` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-165">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="d4e84-166">자세한 내용은 [RFC 7231: Request Cache-Control 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="d4e84-166">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="d4e84-167">`Pragma: no-cache`요청의 헤더는와 동일한 결과를 생성 `Cache-Control: no-cache` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-167">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="d4e84-168">이 헤더는 헤더의 관련 지시문 (있는 경우)에 의해 재정의 됩니다 `Cache-Control` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-168">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="d4e84-169">HTTP/1.0과의 이전 버전과의 호환성을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-169">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="d4e84-170">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-170">The response isn't cached if the header exists.</span></span> <span data-ttu-id="d4e84-171">하나 이상의를 설정 하는 요청 처리 파이프라인의 미들웨어는 응답 cookie 캐싱 미들웨어가 응답을 캐싱하는 것을 방지 합니다 (예: [ cookie 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="d4e84-171">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="d4e84-172">`Vary`헤더는 다른 헤더로 캐시 된 응답을 변경 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-172">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="d4e84-173">예를 들어 헤더를 포함 하 여 인코딩을 통해 응답을 캐시 하 고 헤더를 포함 하 `Vary: Accept-Encoding` 는 요청에 대 한 응답을 캐시 합니다 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-173">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="d4e84-174">헤더 값이 인 응답은 `*` 저장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-174">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="d4e84-175">이 헤더에 의해 부실 하 게 간주 되는 응답은 다른 헤더로 재정의 되지 않는 한 저장 되거나 검색 되지 않습니다 `Cache-Control` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-175">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="d4e84-176">값이이 `*` 고 `ETag` 응답의이 제공 된 값과 일치 하지 않는 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-176">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="d4e84-177">그렇지 않으면 304 (수정 되지 않음) 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-177">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="d4e84-178">헤더가 없으면 캐시 된 `If-None-Match` 응답 날짜가 제공 된 값 보다 최신인 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-178">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="d4e84-179">그렇지 않으면 *304 수정 되지 않은* 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-179">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="d4e84-180">캐시에서 서비스를 제공 하는 경우 `Date` 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-180">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="d4e84-181">캐시에서 서비스를 제공 하는 경우 `Content-Length` 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-181">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="d4e84-182">`Age`원래 응답에서 보낸 헤더는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-182">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="d4e84-183">미들웨어는 캐시 된 응답을 제공할 때 새 값을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-183">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="d4e84-184">캐싱 측면 요청 Cache-Control 지시문</span><span class="sxs-lookup"><span data-stu-id="d4e84-184">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="d4e84-185">미들웨어는 [HTTP 1.1 캐싱 사양의](https://tools.ietf.org/html/rfc7234#section-5.2)규칙을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-185">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="d4e84-186">규칙에는 클라이언트가 보낸 유효한 헤더를 인식 하기 위해 캐시가 필요 `Cache-Control` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-186">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="d4e84-187">사양에 따라 클라이언트는 헤더 값을 사용 하 여 요청을 수행 하 `no-cache` 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-187">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="d4e84-188">현재 미들웨어는 공식 캐싱 사양을 준수 하므로 미들웨어를 사용할 때이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-188">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="d4e84-189">캐싱 동작을 보다 세부적으로 제어 하려면 ASP.NET Core의 다른 캐싱 기능을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-189">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="d4e84-190">다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-190">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="d4e84-191">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d4e84-191">Troubleshooting</span></span>

<span data-ttu-id="d4e84-192">캐싱 동작이 예상과 다른 경우 응답이 캐시 가능 하 고 캐시에서 제공 될 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-192">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="d4e84-193">요청의 들어오는 헤더와 응답의 나가는 헤더를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-193">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="d4e84-194">디버깅에 도움이 되는 [로깅을](xref:fundamentals/logging/index) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-194">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="d4e84-195">캐싱 동작을 테스트 하 고 문제를 해결할 때 브라우저는 원치 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-195">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="d4e84-196">예를 들어, 브라우저에서 페이지를 `Cache-Control` `no-cache` 새로 고칠 때 헤더를 또는로 설정할 수 있습니다 `max-age=0` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-196">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="d4e84-197">다음 도구는 명시적으로 요청 헤더를 설정할 수 있으며 캐싱을 테스트 하는 데 기본 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-197">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="d4e84-198">Fiddler</span><span class="sxs-lookup"><span data-stu-id="d4e84-198">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="d4e84-199">Postman</span><span class="sxs-lookup"><span data-stu-id="d4e84-199">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="d4e84-200">캐싱 조건</span><span class="sxs-lookup"><span data-stu-id="d4e84-200">Conditions for caching</span></span>

* <span data-ttu-id="d4e84-201">요청은 200 (OK) 상태 코드를 포함 하는 서버 응답을 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-201">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="d4e84-202">요청 메서드는 GET 또는 HEAD 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-202">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="d4e84-203">에서 `Startup.Configure` 캐싱은 캐싱을 필요로 하는 미들웨어 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-203">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="d4e84-204">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-204">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="d4e84-205">헤더가 없어야 합니다 `Authorization` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-205">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="d4e84-206">`Cache-Control` 헤더 매개 변수는 유효 해야 하 고 응답은 표시 되 `public` 고 표시 되지 않아야 합니다 `private` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-206">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="d4e84-207">헤더가 있을 `Pragma: no-cache` `Cache-Control` 때 헤더가 헤더를 재정의 하므로 헤더가 없으면 헤더가 없어야 합니다 `Cache-Control` `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-207">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="d4e84-208">헤더가 없어야 합니다 `Set-Cookie` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-208">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="d4e84-209">`Vary` 헤더 매개 변수는 유효 해야 하 고와 같지 않아야 합니다 `*` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-209">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="d4e84-210">`Content-Length`헤더 값 (설정 된 경우)은 응답 본문의 크기와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-210">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="d4e84-211">는 <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-211">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="d4e84-212">`Expires`헤더와 `max-age` 및 cache 지시문에 지정 된 대로 응답이 유효 하지 않아야 합니다 `s-maxage` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-212">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="d4e84-213">응답 버퍼링이 성공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-213">Response buffering must be successful.</span></span> <span data-ttu-id="d4e84-214">응답의 크기는 구성 된 또는 기본값 보다 작아야 합니다 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> .</span><span class="sxs-lookup"><span data-stu-id="d4e84-214">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="d4e84-215">응답의 본문 크기는 구성 된 또는 기본값 보다 작아야 합니다 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .</span><span class="sxs-lookup"><span data-stu-id="d4e84-215">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="d4e84-216">[RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 응답을 캐시할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-216">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="d4e84-217">예를 들어 `no-store` 지시문은 요청 또는 응답 헤더 필드에 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-217">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="d4e84-218">자세한 내용은 섹션 3: [RFC 7234](https://tools.ietf.org/html/rfc7234) 의 *캐시에 응답 저장* 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-218">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="d4e84-219">CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 보안 토큰을 생성 하는 위조 방지 시스템 `Cache-Control` 은 `Pragma` 응답이 캐시 되지 않도록 및 헤더를로 설정 합니다 `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-219">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="d4e84-220">HTML 양식 요소의 위조 방지 토큰을 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은을 참조 하십시오 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .</span><span class="sxs-lookup"><span data-stu-id="d4e84-220">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4e84-221">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d4e84-221">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4e84-222">이 문서에서는 ASP.NET Core 앱에서 응답 캐싱 미들웨어를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-222">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="d4e84-223">미들웨어는 응답을 캐시할 시기를 결정 하 고, 응답을 저장 하 고, 캐시에서 응답을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-223">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="d4e84-224">HTTP 캐싱 및 특성에 대 한 소개는 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) [응답 캐싱](xref:performance/caching/response)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-224">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="d4e84-225">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4e84-225">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="d4e84-226">구성</span><span class="sxs-lookup"><span data-stu-id="d4e84-226">Configuration</span></span>

<span data-ttu-id="d4e84-227">[AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 사용 하거나 [ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 패키지에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-227">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="d4e84-228">에서 `Startup.ConfigureServices` 응답 캐싱 미들웨어를 서비스 컬렉션에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-228">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="d4e84-229"><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>의 요청 처리 파이프라인에 미들웨어를 추가 하는 확장 메서드를 사용 하 여 미들웨어를 사용 하도록 앱을 구성 합니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-229">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="d4e84-230">샘플 앱은 다음 요청에 대 한 캐싱을 제어 하는 헤더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-230">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="d4e84-231">[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2): 최대 10 초 동안 캐시할 수 있는 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-231">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="d4e84-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): 후속 요청의 [수락 인코딩](https://tools.ietf.org/html/rfc7231#section-5.3.4) 헤더가 원래 요청과 일치 하는 경우에만 캐시 된 응답을 제공 하도록 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="d4e84-233">위의 헤더는 응답에 기록 되지 않으며 컨트롤러, 작업 또는 페이지에 대해 재정의 됩니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="d4e84-233">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="d4e84-234">에는 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-234">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="d4e84-235">속성이 설정 되지 않은 경우에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-235">This applies even if a property isn't set.</span></span> <span data-ttu-id="d4e84-236">예를 들어 [Varybyheader](./response.md#vary) 속성을 생략 하면 해당 헤더가 응답에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-236">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="d4e84-237">응답 캐싱 미들웨어는 200 (OK) 상태 코드를 발생 시키는 서버 응답만 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-237">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="d4e84-238">[오류 페이지](xref:fundamentals/error-handling)를 비롯 한 다른 모든 응답은 미들웨어에서 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-238">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="d4e84-239">인증 된 클라이언트에 대 한 콘텐츠를 포함 하는 응답은 미들웨어가 해당 응답을 저장 및 제공 하지 못하도록 캐시할 수 없음으로 표시 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-239">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="d4e84-240">미들웨어가 응답을 캐시할 수 있는지 여부를 결정 하는 방법에 대 한 자세한 내용은 [캐싱에 대 한 조건을](#conditions-for-caching) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-240">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="d4e84-241">옵션</span><span class="sxs-lookup"><span data-stu-id="d4e84-241">Options</span></span>

<span data-ttu-id="d4e84-242">응답 캐싱 옵션은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-242">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="d4e84-243">옵션</span><span class="sxs-lookup"><span data-stu-id="d4e84-243">Option</span></span> | <span data-ttu-id="d4e84-244">설명</span><span class="sxs-lookup"><span data-stu-id="d4e84-244">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="d4e84-245">응답 본문에 대해 캐시할 수 있는 최대 크기 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-245">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="d4e84-246">기본값은 `64 * 1024 * 1024` (64 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-246">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="d4e84-247">응답 캐시 미들웨어의 크기 제한 (바이트)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-247">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="d4e84-248">기본값은 `100 * 1024 * 1024` (100 MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-248">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="d4e84-249">대/소문자를 구분 하는 경로에서 응답이 캐시 되는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-249">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="d4e84-250">기본값은 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-250">The default value is `false`.</span></span> |

<span data-ttu-id="d4e84-251">다음 예제에서는 미들웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-251">The following example configures the middleware to:</span></span>

* <span data-ttu-id="d4e84-252">본문 크기가 1024 바이트 보다 작거나 같은 응답을 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-252">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="d4e84-253">대/소문자 구분 경로를 기준으로 응답을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-253">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="d4e84-254">예를 들어 `/page1` 및 `/Page1` 은 별도로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-254">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="d4e84-255">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="d4e84-255">VaryByQueryKeys</span></span>

<span data-ttu-id="d4e84-256">MVC/web API 컨트롤러 또는 Razor 페이지 페이지 모델을 사용 하는 경우 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 특성은 응답 캐싱에 적합 한 헤더를 설정 하는 데 필요한 매개 변수를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-256">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="d4e84-257">`[ResponseCache]`미들웨어를 엄격히 필요로 하는 특성의 유일한 매개 변수는 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> 실제 HTTP 헤더에 해당 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-257">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="d4e84-258">자세한 내용은 <xref:performance/caching/response#responsecache-attribute>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-258">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="d4e84-259">특성을 사용 하지 않는 경우 `[ResponseCache]` 응답 캐싱은에서 달라질 수 있습니다 `VaryByQueryKeys` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-259">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="d4e84-260"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>다음과 같이 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)에서 직접를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-260">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="d4e84-261">에서와 같은 단일 값을 사용 하는 경우 `*` `VaryByQueryKeys` 모든 요청 쿼리 매개 변수에 의해 캐시가 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-261">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="d4e84-262">응답 캐싱 미들웨어에서 사용 하는 HTTP 헤더</span><span class="sxs-lookup"><span data-stu-id="d4e84-262">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="d4e84-263">다음 표에서는 응답 캐싱에 영향을 주는 HTTP 헤더에 대 한 정보를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-263">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="d4e84-264">header</span><span class="sxs-lookup"><span data-stu-id="d4e84-264">Header</span></span> | <span data-ttu-id="d4e84-265">세부 정보</span><span class="sxs-lookup"><span data-stu-id="d4e84-265">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="d4e84-266">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-266">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="d4e84-267">미들웨어는 cache 지시문으로 표시 된 캐싱 응답만 고려 합니다 `public` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-267">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="d4e84-268">다음 매개 변수를 사용 하 여 캐싱 제어:</span><span class="sxs-lookup"><span data-stu-id="d4e84-268">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="d4e84-269">최대 사용 기간</span><span class="sxs-lookup"><span data-stu-id="d4e84-269">max-age</span></span></li><li><span data-ttu-id="d4e84-270">최대-오래 된&#8224;</span><span class="sxs-lookup"><span data-stu-id="d4e84-270">max-stale&#8224;</span></span></li><li><span data-ttu-id="d4e84-271">최소-새로</span><span class="sxs-lookup"><span data-stu-id="d4e84-271">min-fresh</span></span></li><li><span data-ttu-id="d4e84-272">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="d4e84-272">must-revalidate</span></span></li><li><span data-ttu-id="d4e84-273">no-cache</span><span class="sxs-lookup"><span data-stu-id="d4e84-273">no-cache</span></span></li><li><span data-ttu-id="d4e84-274">저장소 없음</span><span class="sxs-lookup"><span data-stu-id="d4e84-274">no-store</span></span></li><li><span data-ttu-id="d4e84-275">-인 경우에만 캐시</span><span class="sxs-lookup"><span data-stu-id="d4e84-275">only-if-cached</span></span></li><li><span data-ttu-id="d4e84-276">private</span><span class="sxs-lookup"><span data-stu-id="d4e84-276">private</span></span></li><li><span data-ttu-id="d4e84-277">public</span><span class="sxs-lookup"><span data-stu-id="d4e84-277">public</span></span></li><li><span data-ttu-id="d4e84-278">s-maxage</span><span class="sxs-lookup"><span data-stu-id="d4e84-278">s-maxage</span></span></li><li><span data-ttu-id="d4e84-279">프록시-유효성 검사&#8225;</span><span class="sxs-lookup"><span data-stu-id="d4e84-279">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="d4e84-280">&#8224;에 대 한 제한이 지정 되지 않은 경우 `max-stale` 미들웨어는 아무 작업도 수행 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-280">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="d4e84-281">&#8225;는 `proxy-revalidate` 와 동일한 효과가 있습니다 `must-revalidate` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-281">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="d4e84-282">자세한 내용은 [RFC 7231: Request Cache-Control 지시문](https://tools.ietf.org/html/rfc7234#section-5.2.1)을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="d4e84-282">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="d4e84-283">`Pragma: no-cache`요청의 헤더는와 동일한 결과를 생성 `Cache-Control: no-cache` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-283">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="d4e84-284">이 헤더는 헤더의 관련 지시문 (있는 경우)에 의해 재정의 됩니다 `Cache-Control` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-284">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="d4e84-285">HTTP/1.0과의 이전 버전과의 호환성을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-285">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="d4e84-286">헤더가 있으면 응답이 캐시 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-286">The response isn't cached if the header exists.</span></span> <span data-ttu-id="d4e84-287">하나 이상의를 설정 하는 요청 처리 파이프라인의 미들웨어는 응답 cookie 캐싱 미들웨어가 응답을 캐싱하는 것을 방지 합니다 (예: [ cookie 기반 TempData 공급자](xref:fundamentals/app-state#tempdata)).</span><span class="sxs-lookup"><span data-stu-id="d4e84-287">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="d4e84-288">`Vary`헤더는 다른 헤더로 캐시 된 응답을 변경 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-288">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="d4e84-289">예를 들어 헤더를 포함 하 여 인코딩을 통해 응답을 캐시 하 고 헤더를 포함 하 `Vary: Accept-Encoding` 는 요청에 대 한 응답을 캐시 합니다 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-289">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="d4e84-290">헤더 값이 인 응답은 `*` 저장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-290">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="d4e84-291">이 헤더에 의해 부실 하 게 간주 되는 응답은 다른 헤더로 재정의 되지 않는 한 저장 되거나 검색 되지 않습니다 `Cache-Control` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-291">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="d4e84-292">값이이 `*` 고 `ETag` 응답의이 제공 된 값과 일치 하지 않는 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-292">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="d4e84-293">그렇지 않으면 304 (수정 되지 않음) 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-293">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="d4e84-294">헤더가 없으면 캐시 된 `If-None-Match` 응답 날짜가 제공 된 값 보다 최신인 경우 전체 응답이 캐시에서 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-294">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="d4e84-295">그렇지 않으면 *304 수정 되지 않은* 응답이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-295">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="d4e84-296">캐시에서 서비스를 제공 하는 경우 `Date` 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-296">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="d4e84-297">캐시에서 서비스를 제공 하는 경우 `Content-Length` 헤더는 원래 응답에서 제공 되지 않은 경우 미들웨어에 의해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-297">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="d4e84-298">`Age`원래 응답에서 보낸 헤더는 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-298">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="d4e84-299">미들웨어는 캐시 된 응답을 제공할 때 새 값을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-299">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="d4e84-300">캐싱 측면 요청 Cache-Control 지시문</span><span class="sxs-lookup"><span data-stu-id="d4e84-300">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="d4e84-301">미들웨어는 [HTTP 1.1 캐싱 사양의](https://tools.ietf.org/html/rfc7234#section-5.2)규칙을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-301">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="d4e84-302">규칙에는 클라이언트가 보낸 유효한 헤더를 인식 하기 위해 캐시가 필요 `Cache-Control` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-302">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="d4e84-303">사양에 따라 클라이언트는 헤더 값을 사용 하 여 요청을 수행 하 `no-cache` 고 서버에서 모든 요청에 대 한 새 응답을 생성 하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-303">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="d4e84-304">현재 미들웨어는 공식 캐싱 사양을 준수 하므로 미들웨어를 사용할 때이 캐싱 동작에 대 한 개발자 제어는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-304">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="d4e84-305">캐싱 동작을 보다 세부적으로 제어 하려면 ASP.NET Core의 다른 캐싱 기능을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-305">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="d4e84-306">다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-306">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="d4e84-307">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d4e84-307">Troubleshooting</span></span>

<span data-ttu-id="d4e84-308">캐싱 동작이 예상과 다른 경우 응답이 캐시 가능 하 고 캐시에서 제공 될 수 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-308">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="d4e84-309">요청의 들어오는 헤더와 응답의 나가는 헤더를 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-309">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="d4e84-310">디버깅에 도움이 되는 [로깅을](xref:fundamentals/logging/index) 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-310">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="d4e84-311">캐싱 동작을 테스트 하 고 문제를 해결할 때 브라우저는 원치 않는 방식으로 캐싱에 영향을 주는 요청 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-311">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="d4e84-312">예를 들어, 브라우저에서 페이지를 `Cache-Control` `no-cache` 새로 고칠 때 헤더를 또는로 설정할 수 있습니다 `max-age=0` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-312">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="d4e84-313">다음 도구는 명시적으로 요청 헤더를 설정할 수 있으며 캐싱을 테스트 하는 데 기본 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-313">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="d4e84-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="d4e84-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="d4e84-315">Postman</span><span class="sxs-lookup"><span data-stu-id="d4e84-315">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="d4e84-316">캐싱 조건</span><span class="sxs-lookup"><span data-stu-id="d4e84-316">Conditions for caching</span></span>

* <span data-ttu-id="d4e84-317">요청은 200 (OK) 상태 코드를 포함 하는 서버 응답을 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-317">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="d4e84-318">요청 메서드는 GET 또는 HEAD 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-318">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="d4e84-319">에서 `Startup.Configure` 캐싱은 캐싱을 필요로 하는 미들웨어 앞에 배치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-319">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="d4e84-320">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-320">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="d4e84-321">헤더가 없어야 합니다 `Authorization` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-321">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="d4e84-322">`Cache-Control` 헤더 매개 변수는 유효 해야 하 고 응답은 표시 되 `public` 고 표시 되지 않아야 합니다 `private` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-322">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="d4e84-323">헤더가 있을 `Pragma: no-cache` `Cache-Control` 때 헤더가 헤더를 재정의 하므로 헤더가 없으면 헤더가 없어야 합니다 `Cache-Control` `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-323">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="d4e84-324">헤더가 없어야 합니다 `Set-Cookie` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-324">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="d4e84-325">`Vary` 헤더 매개 변수는 유효 해야 하 고와 같지 않아야 합니다 `*` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-325">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="d4e84-326">`Content-Length`헤더 값 (설정 된 경우)은 응답 본문의 크기와 일치 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-326">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="d4e84-327">는 <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-327">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="d4e84-328">`Expires`헤더와 `max-age` 및 cache 지시문에 지정 된 대로 응답이 유효 하지 않아야 합니다 `s-maxage` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-328">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="d4e84-329">응답 버퍼링이 성공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-329">Response buffering must be successful.</span></span> <span data-ttu-id="d4e84-330">응답의 크기는 구성 된 또는 기본값 보다 작아야 합니다 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> .</span><span class="sxs-lookup"><span data-stu-id="d4e84-330">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="d4e84-331">응답의 본문 크기는 구성 된 또는 기본값 보다 작아야 합니다 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .</span><span class="sxs-lookup"><span data-stu-id="d4e84-331">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="d4e84-332">[RFC 7234](https://tools.ietf.org/html/rfc7234) 사양에 따라 응답을 캐시할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-332">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="d4e84-333">예를 들어 `no-store` 지시문은 요청 또는 응답 헤더 필드에 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d4e84-333">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="d4e84-334">자세한 내용은 섹션 3: [RFC 7234](https://tools.ietf.org/html/rfc7234) 의 *캐시에 응답 저장* 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d4e84-334">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="d4e84-335">CSRF (교차 사이트 요청 위조) 공격을 방지 하기 위해 보안 토큰을 생성 하는 위조 방지 시스템 `Cache-Control` 은 `Pragma` 응답이 캐시 되지 않도록 및 헤더를로 설정 합니다 `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="d4e84-335">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="d4e84-336">HTML 양식 요소의 위조 방지 토큰을 사용 하지 않도록 설정 하는 방법에 대 한 자세한 내용은을 참조 하십시오 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .</span><span class="sxs-lookup"><span data-stu-id="d4e84-336">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4e84-337">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="d4e84-337">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end