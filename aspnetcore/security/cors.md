---
title: ASP.NET Core에서 CORS (원본 간 요청) 사용
author: rick-anderson
description: ASP.NET Core 앱에서 원본 간 요청을 허용 하거나 거부 하기 위한 표준으로 CORS를 학습 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 03008f40fc1c4b323535d08a1bb4c4007bc145f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060822"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="88479-103">ASP.NET Core에서 CORS (원본 간 요청) 사용</span><span class="sxs-lookup"><span data-stu-id="88479-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88479-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="88479-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="88479-105">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88479-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="88479-106">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="88479-107">이러한 제한 사항을 *동일 원본 정책* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-107">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="88479-108">동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="88479-109">경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 수행할 수 있도록 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="88479-110">자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/docs/Web/HTTP/CORS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="88479-111">CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="88479-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="88479-112">는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="88479-113">는 CORS 완화 security의 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="88479-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="88479-114">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="88479-115">자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="88479-116">다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="88479-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="88479-118">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88479-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="88479-119">동일한 원점</span><span class="sxs-lookup"><span data-stu-id="88479-119">Same origin</span></span>

<span data-ttu-id="88479-120">동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="88479-121">이러한 두 Url의 출처는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="88479-122">이러한 Url의 원본은 이전 두 Url과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="88479-123">`https://example.net`: 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="88479-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="88479-124">`https://www.example.com/foo.html`: 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="88479-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="88479-125">`http://example.com/foo.html`: 다른 구성표</span><span class="sxs-lookup"><span data-stu-id="88479-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="88479-126">`https://example.com:9000/foo.html`: 다른 포트</span><span class="sxs-lookup"><span data-stu-id="88479-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="88479-127">CORS를 사용하도록 설정</span><span class="sxs-lookup"><span data-stu-id="88479-127">Enable CORS</span></span>

<span data-ttu-id="88479-128">CORS를 사용 하도록 설정 하는 방법에는 세 가지가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="88479-129">미들웨어에서 [명명 된 정책](#np) 또는 [기본 정책을](#dp)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="88479-130">[끝점 라우팅을](#ecors)사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="88479-131">[[EnableCors]](#attr) 특성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="88479-132">명명 된 정책에서 [[EnableCors]](#attr) 특성을 사용 하면 CORS를 지 원하는 끝점을 제한 하는 가장 세밀 한 제어를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="88479-133"><xref:Owin.CorsExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>을 사용 하는 경우 먼저 `UseResponseCaching` 를 호출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="88479-134">각 접근 방식은 다음 섹션에 자세히 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="88479-135">명명 된 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="88479-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="88479-136">CORS 미들웨어는 크로스-원본 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="88479-137">다음 코드는 지정 된 원본을 사용 하 여 모든 앱의 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="88479-138">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="88479-138">The preceding code:</span></span>

* <span data-ttu-id="88479-139">정책 이름을로 설정 `_myAllowSpecificOrigins` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="88479-140">정책 이름은 임의로입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="88479-141"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>확장 메서드를 호출 하 고 `_myAllowSpecificOrigins` CORS 정책을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="88479-142">`UseCors` CORS 미들웨어를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="88479-143">에 대 `UseCors` 한 호출은 앞에 배치 해야 `UseRouting` `UseAuthorization` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="88479-144">자세한 내용은 [미들웨어 순서](xref:fundamentals/middleware/index#middleware-order)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="88479-145"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)사용 하 여를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="88479-146">람다는 개체를 사용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="88479-147">등의 [구성 옵션](#cors-policy-options)에 `WithOrigins` 대해서는이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="88479-148">`_myAllowSpecificOrigins`모든 컨트롤러 끝점에 대해 CORS 정책을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="88479-149">특정 끝점에 CORS 정책을 적용 하려면 [끝점 라우팅](#ecors) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="88479-150">[응답 캐싱 미들웨어](xref:performance/caching/middleware)를 사용 하는 경우 앞에서를 호출 <xref:Owin.CorsExtensions.UseCors%2A> <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="88479-151">끝점 라우팅을 사용 하 여 및에 대 한 호출 사이에 CORS 미들웨어를 실행 하도록 구성 **해야 합니다** `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="88479-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="88479-152">이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="88479-153"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="88479-154">자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="88479-155"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="88479-156">참고: 지정 된 URL은 후행 슬래시 ()를 포함 **하지** 않아야 합니다 `/` .</span><span class="sxs-lookup"><span data-stu-id="88479-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="88479-157">URL이로 종료 되 면 `/` 비교가 반환 `false` 되 고 헤더는 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="88479-158">기본 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="88479-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="88479-159">다음 강조 표시 된 코드는 기본 CORS 정책을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="88479-160">위의 코드는 모든 컨트롤러 끝점에 기본 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="88479-161">엔드포인트 라우팅을 사용하여 Cors 사용</span><span class="sxs-lookup"><span data-stu-id="88479-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="88479-162">현재를 사용 하 여 끝점 별로 CORS를 사용 하도록 설정 하면 `RequireCors` [자동 실행 전 요청](#apf)을 지원 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="88479-163">자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/aspnetcore/issues/20709) 및 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [httpoptions]](#tcer)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="88479-164">끝점 라우팅을 사용 하면 확장 메서드 집합을 사용 하 여 끝점 별로 CORS를 사용 하도록 설정할 수 있습니다 <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> .</span><span class="sxs-lookup"><span data-stu-id="88479-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="88479-165">앞의 코드에서 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-165">In the preceding code:</span></span>

* <span data-ttu-id="88479-166">`app.UseCors` CORS 미들웨어를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="88479-167">기본 정책은 구성 되지 않았기 때문에 `app.UseCors()` CORS를 사용 하도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="88479-168">`/echo`및 컨트롤러 끝점은 지정 된 정책을 사용 하 여 원본 간 요청을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="88479-169">`/echo2` Razor 기본 정책을 지정 하지 않았으므로 및 페이지 끝점에서 원본 간 요청을 허용 **하지 않습니다** .</span><span class="sxs-lookup"><span data-stu-id="88479-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="88479-170">[[DisableCors]](#dc) 특성은를 **사용 하** 여 끝점 라우팅을 통해 설정 된 CORS를 사용 하지 않도록 설정 `RequireCors` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="88479-171">위와 비슷한 코드 테스트에 대 한 지침은 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]](#tcer) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="88479-172">특성과 함께 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="88479-172">Enable CORS with attributes</span></span>

<span data-ttu-id="88479-173">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성으로 cors를 사용 하도록 설정 하 고 cors가 필요한 끝점에만 명명 된 정책을 적용 하면 가장 많이 사용 되는 컨트롤이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="88479-174">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="88479-175">`[EnableCors]`특성은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="88479-176">`[EnableCors]` 기본 정책을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="88479-177">`[EnableCors("{Policy String}")]` 명명 된 정책을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="88479-178">`[EnableCors]`특성은 다음에 적용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="88479-179">Razor 페이지 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="88479-179">Razor Page `PageModel`</span></span>
* <span data-ttu-id="88479-180">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="88479-180">Controller</span></span>
* <span data-ttu-id="88479-181">컨트롤러 동작 방법</span><span class="sxs-lookup"><span data-stu-id="88479-181">Controller action method</span></span>

<span data-ttu-id="88479-182">특성을 사용 하는 컨트롤러, 페이지 모델 또는 작업 메서드에 다른 정책을 적용할 수 있습니다 `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="88479-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="88479-183">`[EnableCors]`특성이 컨트롤러, 페이지 모델 또는 동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 하도록 설정 된 경우 **두 정책이 모두** 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="88479-184">**정책을 결합 하지 않는 것이 좋습니다.** `[EnableCors]` **동일한 앱에서 특성 또는 미들웨어를 사용 합니다.**</span><span class="sxs-lookup"><span data-stu-id="88479-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="88479-185">다음 코드는 각 메서드에 다른 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="88479-186">다음 코드는 두 개의 CORS 정책을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88479-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="88479-187">CORS 요청 제한의 가장 적합 한 제어:</span><span class="sxs-lookup"><span data-stu-id="88479-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="88479-188">명명 된 정책에를 사용 `[EnableCors("MyPolicy")]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="88479-189">기본 정책을 정의 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-189">Don't define a default policy.</span></span>
* <span data-ttu-id="88479-190">[끝점 라우팅을](#ecors)사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="88479-191">다음 섹션의 코드는 위의 목록을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="88479-192">이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="88479-193">CORS 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="88479-193">Disable CORS</span></span>

<span data-ttu-id="88479-194">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 [끝점 라우팅으로](#ecors)설정 된 CORS를 사용 하지 않도록 설정 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="88479-195">다음 코드에서는 CORS 정책을 정의 합니다 `"MyPolicy"` .</span><span class="sxs-lookup"><span data-stu-id="88479-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="88479-196">다음 코드는 작업에 대해 CORS를 사용 하지 않도록 설정 합니다 `GetValues2` .</span><span class="sxs-lookup"><span data-stu-id="88479-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="88479-197">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="88479-197">The preceding code:</span></span>

* <span data-ttu-id="88479-198">[끝점 라우팅을](#ecors)사용 하 여 CORS를 사용 하도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="88479-199">[기본 CORS 정책을](#dp)정의 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="88479-200">[[EnableCors ("MyPolicy")]](#attr) 를 사용 하 여 `"MyPolicy"` 컨트롤러에 대 한 CORS 정책을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="88479-201">메서드에 대해 CORS를 사용 하지 않도록 설정 `GetValues2` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="88479-202">위의 코드를 테스트 하는 방법에 대 한 지침은 [CORS 테스트](#testc) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="88479-203">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="88479-203">CORS policy options</span></span>

<span data-ttu-id="88479-204">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="88479-205">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="88479-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="88479-206">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="88479-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="88479-207">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="88479-208">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="88479-209">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="88479-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="88479-210">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="88479-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="88479-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 는에서 호출 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88479-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="88479-212">일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="88479-213">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="88479-213">Set the allowed origins</span></span>

<span data-ttu-id="88479-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 모든 원본에서 임의의 스키마 (또는)로 CORS 요청을 허용 `http` `https` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="88479-215">`AllowAnyOrigin`*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="88479-216">`AllowAnyOrigin`및 지정 `AllowCredentials` 은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="88479-217">앱이 두 가지 방법으로 구성 된 경우 CORS 서비스는 잘못 된 CORS 응답을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="88479-218">`AllowAnyOrigin` 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Origin` .</span><span class="sxs-lookup"><span data-stu-id="88479-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="88479-219">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="88479-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인에 대 한 원본을 허용 하는 함수로 정책의 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="88479-221">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="88479-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="88479-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="88479-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="88479-223">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="88479-224">실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Methods` .</span><span class="sxs-lookup"><span data-stu-id="88479-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="88479-225">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="88479-226">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-226">Set the allowed request headers</span></span>

<span data-ttu-id="88479-227">[작성자 요청 헤더](https://xhr.spec.whatwg.org/#request)라는 CORS 요청에서 특정 헤더를 보낼 수 있게 하려면를 호출 하 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 고 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="88479-228">모든 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .</span><span class="sxs-lookup"><span data-stu-id="88479-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="88479-229">`AllowAnyHeader` 실행 전 요청 및 [액세스 제어 요청 헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 헤더에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88479-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="88479-230">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="88479-231">로 지정 된 특정 헤더와 일치 하는 CORS 미들웨어 정책은에 지정 된 헤더와 정확 하 게 일치 하는 `WithHeaders` 경우에만 가능 합니다 `Access-Control-Request-Headers` `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="88479-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="88479-232">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="88479-233">CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청을 거부 `Content-Language` [합니다 (Headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="88479-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="88479-234">앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="88479-235">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="88479-236">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-236">Set the exposed response headers</span></span>

<span data-ttu-id="88479-237">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="88479-238">자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="88479-239">기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="88479-240">CORS 사양은 이러한 헤더 *단순 응답 헤더* 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-240">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="88479-241">앱에서 다른 헤더를 사용할 수 있도록 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="88479-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="88479-242">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="88479-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="88479-243">자격 증명을 CORS 요청에서 특수 하 게 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="88479-244">기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="88479-245">자격 증명 cookie 에는 및 HTTP 인증 스키마가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="88479-246">원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는를로 설정 해야 합니다 `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="88479-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="88479-247">`XMLHttpRequest`직접 사용:</span><span class="sxs-lookup"><span data-stu-id="88479-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="88479-248">JQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="88479-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="88479-249">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용:</span><span class="sxs-lookup"><span data-stu-id="88479-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="88479-250">서버에서 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-250">The server must allow the credentials.</span></span> <span data-ttu-id="88479-251">원본 간 자격 증명을 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> .</span><span class="sxs-lookup"><span data-stu-id="88479-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="88479-252">HTTP 응답에는 `Access-Control-Allow-Credentials` 서버에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 헤더가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="88479-253">브라우저에서 자격 증명을 전송 하지만 응답에 유효한 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Credentials` 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="88479-254">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="88479-255">다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="88479-256">CORS 사양은 `"*"` 헤더가 있는 경우 원본으로 설정 (모든 원본)을 사용할 수 없다는 것을 `Access-Control-Allow-Credentials` 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="88479-257">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="88479-257">Preflight requests</span></span>

<span data-ttu-id="88479-258">일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 [옵션](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="88479-259">이 요청을 실행 [전 요청](https://developer.mozilla.org/docs/Glossary/Preflight_request)이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="88479-260">다음 조건이 **모두** 충족 되는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="88479-261">요청 메서드는 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="88479-262">앱은 `Accept` ,, `Accept-Language` `Content-Language` , `Content-Type` 또는 이외의 요청 헤더를 설정 하지 않습니다 `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="88479-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="88479-263">`Content-Type`헤더 (설정 된 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="88479-264">클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 응용 프로그램에서 개체에 대해를 호출 하 여 설정 하는 헤더에 적용 됩니다 `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="88479-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="88479-265">CORS 사양은 이러한 헤더 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="88479-266">이 규칙은 브라우저에서 설정할 수 있는 헤더 (예:, 또는)에는 적용 되지 않습니다 `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="88479-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="88479-267">다음은이 문서의 [CORS 테스트](#testc) 섹션에서 **[Put test]** 단추를 통해 수행 된 실행 전 요청과 유사한 응답의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="88479-268">실행 전 요청은 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="88479-269">여기에는 다음 헤더가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-269">It may include the following headers:</span></span>

* <span data-ttu-id="88479-270">[액세스 제어-요청 메서드](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): 실제 요청에 사용 되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="88479-271">[액세스 제어-요청 헤더](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="88479-272">앞에서 설명한 것 처럼 브라우저에서 설정 하는 헤더 (예:)는 여기에 포함 되지 않습니다 `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="88479-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="88479-273">액세스 제어-허용 방법</span><span class="sxs-lookup"><span data-stu-id="88479-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="88479-274">실행 전 요청이 거부 되 면 앱은 응답을 반환 `200 OK` 하지만 CORS 헤더를 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="88479-275">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="88479-276">거부 된 실행 전 요청에 대 한 예는이 문서의 [CORS 테스트](#testc) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="88479-277">콘솔 앱은 F12 도구를 사용 하 여 브라우저에 따라 다음과 같은 오류를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="88479-278">Firefox: 크로스-원본 요청 차단 됨: 동일한 원본 정책이에서 원격 리소스를 읽을 것을 허용 하지 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="88479-279">(원인: CORS 요청이 실패 했습니다.)</span><span class="sxs-lookup"><span data-stu-id="88479-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="88479-280">자세한 내용</span><span class="sxs-lookup"><span data-stu-id="88479-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="88479-281">Chromium based: ' ' 원본에서 ' '의 fetch에 대 한 액세스가 https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS 정책에 의해 차단 되었습니다. 실행 전 요청에 대 한 응답은 액세스 제어 확인을 통과 하지 않습니다. 요청한 리소스에 ' 액세스 제어-허용-원본 ' 헤더가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="88479-282">사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="88479-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="88479-283">특정 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="88479-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="88479-284">모든 [작성자 요청 헤더](https://www.w3.org/TR/cors/#author-request-headers)를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .</span><span class="sxs-lookup"><span data-stu-id="88479-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="88479-285">브라우저의 설정 방법은 일관 되지 않습니다 `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="88479-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="88479-286">다음 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-286">If either:</span></span>

* <span data-ttu-id="88479-287">헤더는 다음 이외의 값으로 설정 됩니다. `"*"`</span><span class="sxs-lookup"><span data-stu-id="88479-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="88479-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 가 호출 됩니다 .에는 `Accept` , `Content-Type` 및를 포함 하 고 `Origin` 지원 하려는 사용자 지정 헤더를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="88479-289">자동 실행 전 요청 코드</span><span class="sxs-lookup"><span data-stu-id="88479-289">Automatic preflight request code</span></span>

<span data-ttu-id="88479-290">CORS 정책이 적용 되는 경우 다음 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="88479-291">에서를 호출 하 여 전역적으로 `app.UseCors` `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="88479-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="88479-292">특성을 사용 `[EnableCors]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="88479-293">ASP.NET Core는 실행 전 옵션 요청에 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="88479-294">현재를 사용 하 여 끝점 별로 CORS를 사용 하도록 설정 하면 `RequireCors` 자동 실행 전 요청을 지원 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="88479-295">이 문서의 [CORS 테스트](#testc) 섹션에서는이 동작을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88479-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="88479-296">[HttpOptions] 실행 전 요청에 대 한 특성</span><span class="sxs-lookup"><span data-stu-id="88479-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="88479-297">적절 한 정책으로 CORS를 사용 하도록 설정 하면 ASP.NET Core 일반적으로 CORS 실행 전 요청에 자동으로 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="88479-298">일부 시나리오에서는 그렇지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="88479-299">예를 들어 [끝점 라우팅과 함께 CORS](#ecors)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="88479-300">다음 코드는 [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 특성을 사용 하 여 OPTIONS 요청에 대 한 끝점을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88479-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="88479-301">위의 코드를 테스트 하는 방법에 대 한 지침은 [끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]](#tcer) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="88479-302">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="88479-302">Set the preflight expiration time</span></span>

<span data-ttu-id="88479-303">헤더는 실행 `Access-Control-Max-Age` 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="88479-304">이 헤더를 설정 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> .</span><span class="sxs-lookup"><span data-stu-id="88479-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="88479-305">CORS 작동 방법</span><span class="sxs-lookup"><span data-stu-id="88479-305">How CORS works</span></span>

<span data-ttu-id="88479-306">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="88479-307">CORS는 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="88479-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="88479-308">CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="88479-309">예를 들어 악의적인 행위자는 사이트에 대해 [XSS (교차 사이트 스크립팅)](xref:security/cross-site-scripting) 를 사용 하 고 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행 하 여 정보를 도용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="88479-310">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="88479-311">CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="88479-312">서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="88479-313">예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="88479-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="88479-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="88479-315">Postman</span><span class="sxs-lookup"><span data-stu-id="88479-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="88479-316">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="88479-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="88479-317">주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="88479-318">서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="88479-319">CORS가 없는 브라우저는 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="88479-320">CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="88479-321">JSONP는 XHR을 사용 하지 않으며 태그를 사용 하 여 `<script>` 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="88479-322">스크립트를 크로스-원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="88479-323">[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="88479-324">브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="88479-325">CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="88479-326">배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) 에 대 한 [테스트 배치 단추](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="88479-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="88479-327">다음은 [값](https://cors3.azurewebsites.net/) 테스트 단추에서로의 원본 간 요청 예제입니다 `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="88479-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="88479-328">`Origin`헤더:</span><span class="sxs-lookup"><span data-stu-id="88479-328">The `Origin` header:</span></span>

* <span data-ttu-id="88479-329">요청을 수행 하는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="88479-330">는 필수 이며 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="88479-331">**일반 헤더**</span><span class="sxs-lookup"><span data-stu-id="88479-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="88479-332">**응답 헤더**</span><span class="sxs-lookup"><span data-stu-id="88479-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="88479-333">**요청 헤더**</span><span class="sxs-lookup"><span data-stu-id="88479-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="88479-334">`OPTIONS`요청에서 서버는 응답에 **응답 헤더** 헤더를 설정 합니다 `Access-Control-Allow-Origin: {allowed origin}` .</span><span class="sxs-lookup"><span data-stu-id="88479-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="88479-335">예를 들어, 배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)인 [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) 단추 `OPTIONS` 요청은 다음 헤더를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="88479-336">**일반 헤더**</span><span class="sxs-lookup"><span data-stu-id="88479-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="88479-337">**응답 헤더**</span><span class="sxs-lookup"><span data-stu-id="88479-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="88479-338">**요청 헤더**</span><span class="sxs-lookup"><span data-stu-id="88479-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="88479-339">위의 **응답 헤더** 에서 서버는 응답에 [액세스 제어 허용 원본](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-339">In the preceding **Response headers** , the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="88479-340">`https://cors1.azurewebsites.net`이 헤더의 값은 요청의 헤더와 일치 합니다 `Origin` .</span><span class="sxs-lookup"><span data-stu-id="88479-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="88479-341"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>가 호출 되 면 `Access-Control-Allow-Origin: *` 와일드 카드 값이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="88479-342">`AllowAnyOrigin` 모든 원본을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="88479-343">응답에 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Origin` 원본 간 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="88479-344">특히 브라우저에서 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="88479-345">서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="88479-346">표시 옵션 요청</span><span class="sxs-lookup"><span data-stu-id="88479-346">Display OPTIONS requests</span></span>

<span data-ttu-id="88479-347">기본적으로 Chrome 및 Edge 브라우저는 F12 도구의 네트워크 탭에서 옵션 요청을 표시 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="88479-348">이러한 브라우저에 옵션 요청을 표시 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="88479-349">`chrome://flags/#out-of-blink-cors` 또는 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="88479-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="88479-350">플래그를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-350">disable the flag.</span></span>
* <span data-ttu-id="88479-351">시작할지.</span><span class="sxs-lookup"><span data-stu-id="88479-351">restart.</span></span>

<span data-ttu-id="88479-352">Firefox에는 기본적으로 옵션 요청이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="88479-353">IIS의 CORS</span><span class="sxs-lookup"><span data-stu-id="88479-353">CORS in IIS</span></span>

<span data-ttu-id="88479-354">IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="88479-355">이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="88479-356">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="88479-356">Test CORS</span></span>

<span data-ttu-id="88479-357">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) 에는 CORS를 테스트 하는 코드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="88479-358">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="88479-359">이 샘플은 페이지가 추가 된 API 프로젝트입니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="88479-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="88479-360">`WithOrigins("https://localhost:<port>");`[다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="88479-361">다음은 `ValuesController` 테스트에 대 한 끝점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="88479-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) 는 RouteInfo NuGet 패키지 [Rick.Doc](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) 에서 제공 하며 경로 정보를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="88479-363">다음 방법 중 하나를 사용 하 여 앞의 샘플 코드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="88479-364">에서 배포 된 샘플 앱을 사용 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="88479-365">샘플을 다운로드할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="88479-366">`dotnet run`의 기본 URL을 사용 하 여 샘플을 실행 합니다 `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="88479-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="88479-367">URL에 대해 44398로 설정 된 포트를 사용 하 여 Visual Studio에서 샘플을 실행 합니다 `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="88479-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="88479-368">F12 도구를 사용 하 여 브라우저 사용:</span><span class="sxs-lookup"><span data-stu-id="88479-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="88479-369">**값** 단추를 선택 하 고 **네트워크** 탭에서 헤더를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="88479-370">**테스트 배치** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-370">Select the **PUT test** button.</span></span> <span data-ttu-id="88479-371">옵션 요청 표시에 대 한 지침은 [옵션 요청 표시](#options) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="88479-372">**Put 테스트** 는 옵션 실행 전 요청 및 PUT 요청 이라는 두 개의 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88479-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="88479-373">**`GetValues2 [DisableCors]`** 실패 한 CORS 요청을 트리거하는 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="88479-374">문서에 설명 된 것 처럼 응답은 200 성공을 반환 하지만 CORS 요청은 수행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="88479-375">CORS 오류를 보려면 **콘솔** 탭을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="88479-376">브라우저에 따라 다음과 같은 오류가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="88479-377">원본에서 가져오기에 대 한 액세스가 `'https://cors1.azurewebsites.net/api/values/GetValues2'` `'https://cors3.azurewebsites.net'` CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어-원본 ' 헤더가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="88479-378">사용자 요구에 불명확한 응답이 제공되면 요청 모드를 'no-cors'로 설정하여 CORS가 사용되지 않도록 설정된 리소스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="88479-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="88479-379">CORS 사용 끝점은 [말아](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="88479-380">도구를 사용 하는 경우 헤더에 의해 지정 된 요청의 원점은 `Origin` 요청을 받는 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="88479-381">요청이 헤더의 값을 기반으로 하는 *교차 원본이* 아닌 경우 `Origin` :</span><span class="sxs-lookup"><span data-stu-id="88479-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="88479-382">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="88479-383">CORS 헤더는 응답에서 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="88479-384">다음 명령은를 사용 `curl` 하 여 정보를 포함 하는 옵션 요청을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="88479-385">끝점 라우팅을 사용 하 여 CORS 테스트 및 [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="88479-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="88479-386">현재를 사용 하 여 끝점 별로 CORS를 사용 하도록 설정 하면 `RequireCors` [자동 실행 전 요청](#apf)을 지원 **하지** 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="88479-387">끝점 라우팅을 사용 하 여 [CORS를 사용 하도록 설정 하](#ecors)는 다음 코드를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="88479-388">`TodoItems1Controller`테스트를 위한 끝점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="88479-389">배포 된 [샘플](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)의 [테스트 페이지](https://cors1.azurewebsites.net/test?number=1) 에서 이전 코드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="88479-390">끝점이 실행 전 요청에 응답 하 고이 요청에 응답 하기 때문에 **Delete [EnableCors]** 및 **GET [EnableCors]** 단추가 성공 합니다 `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="88479-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="88479-391">다른 끝점은 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-391">The other endpoints fails.</span></span> <span data-ttu-id="88479-392">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) 가 다음을 보내기 때문에 **GET** 단추가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="88479-393">다음은 `TodoItems2Controller` 비슷한 끝점을 제공 하지만 OPTIONS 요청에 응답 하는 명시적인 코드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="88479-394">배포 된 샘플의 [테스트 페이지](https://cors1.azurewebsites.net/test?number=2) 에서 이전 코드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="88479-395">**컨트롤러** 드롭다운 목록에서 실행 **전** 을 선택 하 고 **컨트롤러를 설정** 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller** .</span></span> <span data-ttu-id="88479-396">끝점에 대 한 모든 CORS 호출이 `TodoItems2Controller` 성공 했습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88479-397">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="88479-397">Additional resources</span></span>

* [<span data-ttu-id="88479-398">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="88479-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="88479-399">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="88479-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="88479-400">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="88479-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="88479-401">이 문서에서는 ASP.NET Core 앱에서 CORS를 사용 하도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="88479-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="88479-402">브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="88479-403">이러한 제한 사항을 *동일 원본 정책* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-403">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="88479-404">동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="88479-405">경우에 따라 다른 사이트에서 앱에 대 한 원본 간 요청을 허용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="88479-406">자세한 내용은 [MOZILLA CORS 문서](https://developer.mozilla.org/docs/Web/HTTP/CORS)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="88479-407">CORS ( [원본 간 리소스 공유](https://www.w3.org/TR/cors/) ):</span><span class="sxs-lookup"><span data-stu-id="88479-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="88479-408">는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="88479-409">는 CORS 완화 security의 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="88479-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="88479-410">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="88479-411">자세한 내용은 [CORS 작동 방식](#how-cors)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="88479-412">다른 서버를 거부 하는 동안 서버에서 일부 교차 원본 요청을 명시적으로 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="88479-413">[JSONP](/dotnet/framework/wcf/samples/jsonp)와 같은 이전 기술 보다 안전 하 고 유연 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="88479-414">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="88479-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="88479-415">동일한 원점</span><span class="sxs-lookup"><span data-stu-id="88479-415">Same origin</span></span>

<span data-ttu-id="88479-416">동일한 스키마, 호스트 및 포트 ([RFC 6454](https://tools.ietf.org/html/rfc6454))가 있는 경우 두 url의 원본이 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="88479-417">이러한 두 Url의 출처는 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="88479-418">이러한 Url의 원본은 이전 두 Url과 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="88479-419">`https://example.net`: 다른 도메인</span><span class="sxs-lookup"><span data-stu-id="88479-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="88479-420">`https://www.example.com/foo.html`: 다른 하위 도메인</span><span class="sxs-lookup"><span data-stu-id="88479-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="88479-421">`http://example.com/foo.html`: 다른 구성표</span><span class="sxs-lookup"><span data-stu-id="88479-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="88479-422">`https://example.com:9000/foo.html`: 다른 포트</span><span class="sxs-lookup"><span data-stu-id="88479-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="88479-423">Internet Explorer는 원본을 비교할 때 포트를 고려 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="88479-424">명명 된 정책 및 미들웨어를 사용 하는 CORS</span><span class="sxs-lookup"><span data-stu-id="88479-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="88479-425">CORS 미들웨어는 크로스-원본 요청을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="88479-426">다음 코드는 지정 된 원본으로 전체 앱에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="88479-427">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="88479-427">The preceding code:</span></span>

* <span data-ttu-id="88479-428">정책 이름을 " \_ myAllowSpecificOrigins"로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="88479-429">정책 이름은 임의로입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="88479-430"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS를 사용 하도록 설정 하는 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="88479-431"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [람다 식을](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)사용 하 여를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="88479-432">람다는 개체를 사용 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="88479-433">등의 [구성 옵션](#cors-policy-options)에 `WithOrigins` 대해서는이 문서의 뒷부분에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="88479-434"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>메서드 호출은 CORS 서비스를 앱의 서비스 컨테이너에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="88479-435">자세한 내용은이 문서의 [CORS 정책 옵션](#cpo) (영문)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="88479-436"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>메서드는 다음 코드와 같이 메서드를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="88479-437">참고: URL은 후행 슬래시 ()를 포함 **하지** 않아야 합니다 `/` .</span><span class="sxs-lookup"><span data-stu-id="88479-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="88479-438">URL이로 종료 되 면 `/` 비교가 반환 `false` 되 고 헤더는 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="88479-439">다음 코드에서는 CORS 미들웨어를 통해 모든 앱 끝점에 CORS 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="88479-440">참고: `UseCors` 이전에를 호출 해야 합니다 `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="88479-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="88479-441">페이지 [ Razor , 컨트롤러 및 작업 메서드에서 Cors를 사용](#ecors) 하 여 페이지/컨트롤러/동작 수준에서 cors 정책 적용을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="88479-442">이전 코드와 유사한 코드 테스트에 대 한 지침은 [CORS 테스트](#test) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="88479-443">특성과 함께 CORS 사용</span><span class="sxs-lookup"><span data-stu-id="88479-443">Enable CORS with attributes</span></span>

<span data-ttu-id="88479-444">[ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 특성은 CORS를 전역적으로 적용 하는 대안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="88479-445">`[EnableCors]`특성은 모든 끝점이 아니라 선택한 끝점에 대해 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="88479-446">`[EnableCors]`기본 정책을 지정 하 고 정책을 지정 하려면를 사용 `[EnableCors("{Policy String}")]` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="88479-447">`[EnableCors]`특성은 다음에 적용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="88479-448">Razor 페이지 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="88479-448">Razor Page `PageModel`</span></span>
* <span data-ttu-id="88479-449">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="88479-449">Controller</span></span>
* <span data-ttu-id="88479-450">컨트롤러 동작 방법</span><span class="sxs-lookup"><span data-stu-id="88479-450">Controller action method</span></span>

<span data-ttu-id="88479-451">특성을 사용 하 여 컨트롤러/페이지-모델/작업에 다른 정책을 적용할 수 있습니다  `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="88479-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="88479-452">`[EnableCors]`특성이 컨트롤러/페이지 모델/동작 메서드에 적용 되 고 CORS가 미들웨어에서 사용 하도록 설정 된 경우 두 정책이 **모두** 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="88479-453">정책을 결합 **하지 않는** 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="88479-454">`[EnableCors]`특성 또는 미들웨어를 사용 합니다 **not both** .</span><span class="sxs-lookup"><span data-stu-id="88479-454">Use the `[EnableCors]` attribute or middleware, **not both** .</span></span> <span data-ttu-id="88479-455">를 사용 하 `[EnableCors]` 는 경우 기본 정책을 정의 **하지** 마십시오.</span><span class="sxs-lookup"><span data-stu-id="88479-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="88479-456">다음 코드는 각 메서드에 다른 정책을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="88479-457">다음 코드에서는 CORS 기본 정책과 라는 정책을 만듭니다 `"AnotherPolicy"` .</span><span class="sxs-lookup"><span data-stu-id="88479-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="88479-458">CORS 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="88479-458">Disable CORS</span></span>

<span data-ttu-id="88479-459">[ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) 특성은 컨트롤러/페이지-모델/작업에 대해 CORS를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="88479-460">CORS 정책 옵션</span><span class="sxs-lookup"><span data-stu-id="88479-460">CORS policy options</span></span>

<span data-ttu-id="88479-461">이 섹션에서는 CORS 정책에서 설정할 수 있는 다양 한 옵션에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="88479-462">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="88479-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="88479-463">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="88479-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="88479-464">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="88479-465">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="88479-466">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="88479-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="88479-467">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="88479-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="88479-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 는에서 호출 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="88479-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="88479-469">일부 옵션의 경우 [CORS 작동 방법](#how-cors) 섹션을 먼저 읽는 것이 도움이 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="88479-470">허용 되는 원본 설정</span><span class="sxs-lookup"><span data-stu-id="88479-470">Set the allowed origins</span></span>

<span data-ttu-id="88479-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: 모든 원본에서 임의의 스키마 (또는)로 CORS 요청을 허용 `http` `https` 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="88479-472">`AllowAnyOrigin`*모든 웹 사이트* 에서 앱에 대 한 원본 간 요청을 수행할 수 있기 때문에은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="88479-473">`AllowAnyOrigin`및 지정 `AllowCredentials` 은 안전 하지 않은 구성으로, 사이트 간 요청 위조를 발생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="88479-474">보안 응용 프로그램의 경우 클라이언트에서 서버 리소스에 액세스할 수 있는 권한을 부여 해야 하는 경우 정확한 원본 목록을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="88479-475">`AllowAnyOrigin` 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Origin` .</span><span class="sxs-lookup"><span data-stu-id="88479-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="88479-476">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="88479-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 원본이 허용 되는지 평가할 때 구성 된 와일드 카드 도메인에 대 한 원본을 허용 하는 함수로 정책의 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="88479-478">허용 되는 HTTP 메서드 설정</span><span class="sxs-lookup"><span data-stu-id="88479-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="88479-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="88479-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="88479-480">모든 HTTP 메서드를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="88479-481">실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Allow-Methods` .</span><span class="sxs-lookup"><span data-stu-id="88479-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="88479-482">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="88479-483">허용 되는 요청 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-483">Set the allowed request headers</span></span>

<span data-ttu-id="88479-484">*작성자 요청 헤더* 라는 CORS 요청에서 특정 헤더를 보낼 수 있게 하려면를 호출 하 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 고 허용 되는 헤더를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-484">To allow specific headers to be sent in a CORS request, called *author request headers* , call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="88479-485">모든 작성자 요청 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .</span><span class="sxs-lookup"><span data-stu-id="88479-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="88479-486">이 설정은 실행 전 요청 및 헤더에 영향을 줍니다 `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="88479-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="88479-487">자세한 내용은 실행 [전 요청](#preflight-requests) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="88479-488">CORS 미들웨어는 항상에 있는 4 개의 헤더 `Access-Control-Request-Headers` 를 CorsPolicy 헤더에 구성 된 값에 관계 없이 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="88479-489">이 헤더 목록에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="88479-490">예를 들어 다음과 같이 구성 된 앱을 살펴보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="88479-491">가 항상 허용 되기 때문에 CORS 미들웨어는 다음 요청 헤더를 사용 하 여 실행 전 요청에 성공적으로 응답 합니다 `Content-Language` .</span><span class="sxs-lookup"><span data-stu-id="88479-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="88479-492">노출 된 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="88479-492">Set the exposed response headers</span></span>

<span data-ttu-id="88479-493">기본적으로 브라우저는 모든 응답 헤더를 앱에 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="88479-494">자세한 내용은 [W3C 원본 간 리소스 공유 (용어): 단순 응답 헤더](https://www.w3.org/TR/cors/#simple-response-header)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="88479-495">기본적으로 사용할 수 있는 응답 헤더는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="88479-496">CORS 사양은 이러한 헤더 *단순 응답 헤더* 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-496">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="88479-497">앱에서 다른 헤더를 사용할 수 있도록 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="88479-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="88479-498">원본 간 요청에 대 한 자격 증명</span><span class="sxs-lookup"><span data-stu-id="88479-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="88479-499">자격 증명을 CORS 요청에서 특수 하 게 처리 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="88479-500">기본적으로 브라우저는 원본 간 요청과 함께 자격 증명을 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="88479-501">자격 증명 cookie 에는 및 HTTP 인증 스키마가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="88479-502">원본 간 요청을 사용 하 여 자격 증명을 보내려면 클라이언트는를로 설정 해야 합니다 `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="88479-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="88479-503">`XMLHttpRequest`직접 사용:</span><span class="sxs-lookup"><span data-stu-id="88479-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="88479-504">JQuery 사용:</span><span class="sxs-lookup"><span data-stu-id="88479-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="88479-505">[FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)사용:</span><span class="sxs-lookup"><span data-stu-id="88479-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="88479-506">서버에서 자격 증명을 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-506">The server must allow the credentials.</span></span> <span data-ttu-id="88479-507">원본 간 자격 증명을 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> .</span><span class="sxs-lookup"><span data-stu-id="88479-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="88479-508">HTTP 응답에는 `Access-Control-Allow-Credentials` 서버에서 원본 간 요청에 대 한 자격 증명을 허용 하도록 브라우저에 지시 하는 헤더가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="88479-509">브라우저에서 자격 증명을 전송 하지만 응답에 유효한 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Credentials` 브라우저에서 앱에 응답을 노출 하지 않으며 크로스-원본 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="88479-510">크로스-원본 자격 증명을 허용 하는 것은 보안상 위험 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="88479-511">다른 도메인의 웹 사이트는 사용자의 지식 없이 사용자를 대신 하 여 사용자의 앱에 로그인 한 사용자의 자격 증명을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="88479-512">CORS 사양은 `"*"` 헤더가 있는 경우 원본으로 설정 (모든 원본)을 사용할 수 없다는 것을 `Access-Control-Allow-Credentials` 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="88479-513">실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="88479-513">Preflight requests</span></span>

<span data-ttu-id="88479-514">일부 CORS 요청의 경우 브라우저는 실제 요청을 만들기 전에 추가 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="88479-515">이 요청을 실행 *전 요청* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-515">This request is called a *preflight request* .</span></span> <span data-ttu-id="88479-516">다음 조건에 해당 하는 경우 브라우저에서 실행 전 요청을 건너뛸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="88479-517">요청 메서드는 GET, HEAD 또는 POST입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="88479-518">앱은 `Accept` ,, `Accept-Language` `Content-Language` , `Content-Type` 또는 이외의 요청 헤더를 설정 하지 않습니다 `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="88479-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="88479-519">`Content-Type`헤더 (설정 된 경우)에는 다음 값 중 하나가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="88479-520">클라이언트 요청에 대해 설정 된 요청 헤더에 대 한 규칙은 응용 프로그램에서 개체에 대해를 호출 하 여 설정 하는 헤더에 적용 됩니다 `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="88479-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="88479-521">CORS 사양은 이러한 헤더 *작성자 요청 헤더* 를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-521">The CORS specification calls these headers *author request headers* .</span></span> <span data-ttu-id="88479-522">이 규칙은 브라우저에서 설정할 수 있는 헤더 (예:, 또는)에는 적용 되지 않습니다 `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="88479-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="88479-523">다음은 실행 전 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="88479-524">사전 비행 요청은 HTTP OPTIONS 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="88479-525">여기에는 두 가지 특수 헤더가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-525">It includes two special headers:</span></span>

* <span data-ttu-id="88479-526">`Access-Control-Request-Method`: 실제 요청에 사용 되는 HTTP 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="88479-527">`Access-Control-Request-Headers`: 앱이 실제 요청에 대해 설정 하는 요청 헤더 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="88479-528">앞에서 설명한 것 처럼 브라우저에서 설정 하는 헤더 (예:)는 여기에 포함 되지 않습니다 `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="88479-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="88479-529">적절 한 정책으로 CORS를 사용 하도록 설정 하면 ASP.NET Core는 일반적으로 CORS 실행 전 요청에 자동으로 응답 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="88479-530">실행 [전 요청에 대 한 [Httpoptions] 특성을](#pro)참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="88479-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="88479-531">CORS 실행 전 요청에는 헤더가 포함 될 수 `Access-Control-Request-Headers` 있으며,이는 실제 요청과 함께 전송 되는 헤더를 서버에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="88479-532">특정 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="88479-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="88479-533">모든 작성자 요청 헤더를 허용 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> .</span><span class="sxs-lookup"><span data-stu-id="88479-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="88479-534">브라우저의 설정 방법은 완전히 일치 하지 않습니다 `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="88479-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="88479-535">헤더를 (또는을 사용 하는 경우) 이외의 값으로 설정 하는 경우, `"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> `Accept` 및를 포함 하 `Content-Type` 고 지원 하려는 사용자 지정 헤더를 포함 해야 합니다 `Origin` .</span><span class="sxs-lookup"><span data-stu-id="88479-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="88479-536">다음은 실행 전 요청에 대 한 응답 예제입니다 (서버에서 요청을 허용 한다고 가정).</span><span class="sxs-lookup"><span data-stu-id="88479-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="88479-537">응답에는 허용 된 `Access-Control-Allow-Methods` 메서드를 나열 하는 헤더와 허용 되는 헤더를 나열 하는 헤더 (선택 사항)가 포함 되어 있습니다 `Access-Control-Allow-Headers` .</span><span class="sxs-lookup"><span data-stu-id="88479-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="88479-538">실행 전 요청이 성공 하면 브라우저에서 실제 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="88479-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="88479-539">실행 전 요청이 거부 되 면 앱은 *200 OK* 응답을 반환 하지만 CORS 헤더는 다시 전송 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="88479-540">따라서 브라우저에서 원본 간 요청을 시도 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="88479-541">실행 전 만료 시간 설정</span><span class="sxs-lookup"><span data-stu-id="88479-541">Set the preflight expiration time</span></span>

<span data-ttu-id="88479-542">헤더는 실행 `Access-Control-Max-Age` 전 요청에 대 한 응답을 캐시할 수 있는 기간을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="88479-543">이 헤더를 설정 하려면 다음을 호출 합니다 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> .</span><span class="sxs-lookup"><span data-stu-id="88479-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="88479-544">CORS 작동 방법</span><span class="sxs-lookup"><span data-stu-id="88479-544">How CORS works</span></span>

<span data-ttu-id="88479-545">이 섹션에서는 HTTP 메시지 수준에서 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 요청에 발생 하는 작업에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="88479-546">CORS는 보안 기능이 **아닙니다** .</span><span class="sxs-lookup"><span data-stu-id="88479-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="88479-547">CORS는 서버에서 동일한 원본 정책을 완화할 수 있게 해 주는 W3C 표준입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="88479-548">예를 들어 악의적인 행위자는 사이트에 대 한 [XSS (교차 사이트 스크립팅) 방지](xref:security/cross-site-scripting) 를 사용 하 여 정보를 도용 하기 위해 CORS 지원 사이트에 대 한 교차 사이트 요청을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="88479-549">API는 CORS를 허용 하 여 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="88479-550">CORS를 적용 하는 것은 클라이언트 (브라우저)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="88479-551">서버는 요청을 실행 하 고 응답을 반환 합니다. 클라이언트는 오류를 반환 하 고 응답을 차단 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="88479-552">예를 들어 다음 도구 중 하나는 서버 응답을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="88479-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="88479-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="88479-554">Postman</span><span class="sxs-lookup"><span data-stu-id="88479-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="88479-555">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="88479-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="88479-556">주소 표시줄에 URL을 입력 하는 웹 브라우저입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="88479-557">서버에서 브라우저가 원본 간 [Xhr](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 또는 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 요청을 실행 하도록 허용 하는 방법입니다. 그렇지 않으면이를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="88479-558">브라우저 (CORS 불포함)는 크로스-원본 요청을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="88479-559">CORS 이전에는 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 을 사용 하 여이 제한을 회피 했습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="88479-560">JSONP는 XHR을 사용 하지 않으며 태그를 사용 하 여 `<script>` 응답을 수신 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="88479-561">스크립트를 크로스-원본으로 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="88479-562">[CORS 사양](https://www.w3.org/TR/cors/) 에는 크로스-원본 요청을 가능 하 게 하는 몇 가지 새로운 HTTP 헤더가 도입 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="88479-563">브라우저에서 CORS를 지 원하는 경우 원본 간 요청에 대해 이러한 헤더를 자동으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="88479-564">CORS를 사용 하도록 설정 하는 데 사용자 지정 JavaScript 코드가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="88479-565">다음은 원본 간 요청의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="88479-566">`Origin`헤더는 요청을 만드는 사이트의 도메인을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="88479-567">`Origin`헤더는 필수 이며 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="88479-568">서버에서 요청을 허용 하는 경우 `Access-Control-Allow-Origin` 응답에 헤더를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="88479-569">이 헤더의 값은 `Origin` 요청의 헤더와 일치 하거나 와일드 카드 값입니다 `"*"` . 즉, 모든 원본이 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="88479-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="88479-570">응답에 헤더가 포함 되지 않은 경우 `Access-Control-Allow-Origin` 원본 간 요청이 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="88479-571">특히 브라우저에서 요청을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="88479-572">서버에서 성공적인 응답을 반환 하는 경우에도 브라우저에서 응답을 클라이언트 앱에 사용할 수 있도록 설정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="88479-573">CORS 테스트</span><span class="sxs-lookup"><span data-stu-id="88479-573">Test CORS</span></span>

<span data-ttu-id="88479-574">CORS를 테스트 하려면:</span><span class="sxs-lookup"><span data-stu-id="88479-574">To test CORS:</span></span>

1. <span data-ttu-id="88479-575">[API 프로젝트를 만듭니다](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="88479-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="88479-576">또는 [샘플을 다운로드할](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="88479-577">이 문서의 방법 중 하나를 사용 하 여 CORS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="88479-578">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="88479-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="88479-579">`WithOrigins("https://localhost:<port>");`[다운로드 샘플 코드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)와 유사한 샘플 앱을 테스트 하는 데만 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="88479-580">웹 앱 프로젝트 ( Razor 페이지 또는 MVC)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="88479-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="88479-581">이 샘플에서는 Razor 페이지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="88479-582">API 프로젝트와 동일한 솔루션에서 웹 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="88479-583">다음 강조 표시 된 코드를 *Index. cshtml* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="88479-584">위의 코드에서를 배포 된 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 앱의 URL로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="88479-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="88479-585">API 프로젝트를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-585">Deploy the API project.</span></span> <span data-ttu-id="88479-586">예를 들어 [Azure에 배포할 수](xref:host-and-deploy/azure-apps/index)있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="88479-587">Razor바탕 화면에서 페이지 또는 MVC 앱을 실행 하 고 **테스트** 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="88479-588">F12 도구를 사용 하 여 오류 메시지를 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="88479-589">에서 localhost 원본을 제거 `WithOrigins` 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="88479-590">또는 다른 포트를 사용 하 여 클라이언트 앱을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="88479-591">예를 들어 Visual Studio에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="88479-592">클라이언트 앱을 사용 하 여 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-592">Test with the client app.</span></span> <span data-ttu-id="88479-593">CORS 오류는 오류를 반환 하지만 JavaScript에는 오류 메시지를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="88479-594">F12 도구에서 콘솔 탭을 사용 하 여 오류를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="88479-595">브라우저에 따라 다음과 비슷한 오류가 표시 됩니다 (F12 도구 콘솔).</span><span class="sxs-lookup"><span data-stu-id="88479-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="88479-596">Microsoft Edge 사용:</span><span class="sxs-lookup"><span data-stu-id="88479-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="88479-597">**SEC7120: [CORS] 원본에서 원본 `https://localhost:44375` `https://localhost:44375` 간 리소스에 대 한 액세스 제어-원본 응답 헤더를 찾을 수 없습니다. `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="88479-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="88479-598">Chrome 사용:</span><span class="sxs-lookup"><span data-stu-id="88479-598">Using Chrome:</span></span>

     <span data-ttu-id="88479-599">**원본에서의 XMLHttpRequest에 대 한 액세스가 `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` CORS 정책에 의해 차단 되었습니다. 요청 된 리소스에 ' 액세스 제어--원본 ' 헤더가 없습니다.**</span><span class="sxs-lookup"><span data-stu-id="88479-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="88479-600">CORS 사용 끝점은 [Fiddler](https://www.telerik.com/fiddler) 또는 [postman](https://www.getpostman.com/)과 같은 도구를 사용 하 여 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="88479-601">도구를 사용 하는 경우 헤더에 의해 지정 된 요청의 원점은 `Origin` 요청을 받는 호스트와 달라 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="88479-602">요청이 헤더의 값을 기반으로 하는 *교차 원본이* 아닌 경우 `Origin` :</span><span class="sxs-lookup"><span data-stu-id="88479-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="88479-603">CORS 미들웨어가 요청을 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="88479-604">CORS 헤더는 응답에서 반환 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="88479-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="88479-605">IIS의 CORS</span><span class="sxs-lookup"><span data-stu-id="88479-605">CORS in IIS</span></span>

<span data-ttu-id="88479-606">IIS에 배포할 때 서버에서 익명 액세스를 허용 하도록 구성 되지 않은 경우 CORS를 Windows 인증 전에 실행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="88479-607">이 시나리오를 지원 하려면 앱에 대해 [IIS CORS 모듈](https://www.iis.net/downloads/microsoft/iis-cors-module) 을 설치 하 고 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="88479-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88479-608">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="88479-608">Additional resources</span></span>

* [<span data-ttu-id="88479-609">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="88479-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="88479-610">IIS CORS 모듈 시작</span><span class="sxs-lookup"><span data-stu-id="88479-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
