---
title: ASP.NET Core에서 URL 재작성 미들웨어
author: rick-anderson
description: ASP.NET Core 애플리케이션에서 URL 재작성 미들웨어로 URL 재작성 및 리디렉션 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: 44e6570090de9c5f3550d18a30fcde758fb16f85
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588525"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="c8559-103">ASP.NET Core에서 URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="c8559-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="c8559-104">작성자: [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="c8559-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c8559-105">본문에서는 ASP.NET Core 응용 프로그램에서 URL 재작성 미들웨어를 사용하는 방법에 관한 지침과 URL 재작성에 관해서 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="c8559-106">URL 재작성은 하나 이상의 미리 정의된 규칙을 기반으로 하는 요청 URL을 수정하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="c8559-107">URL 재작성은 위치와 주소가 밀접하게 연결되지 않도록 리소스 위치와 해당 주소 간의 추상화를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="c8559-108">URL 재작성이 중요한 몇 가지 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="c8559-109">서버 리소스를 일시적 또는 영구적으로 이동하거나 대체하고, 해당 리소스에 대한 안정적인 로케이터를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="c8559-110">여러 앱 또는 한 앱의 여러 영역 간에 요청 처리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="c8559-111">들어오는 요청의 URL 세그먼트를 제거, 추가, 또는 다시 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="c8559-112">SEO(검색 엔진 최적화)에 맞게 공용 URL을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="c8559-113">친숙한 공용 URL을 사용하여 방문자가 리소스를 요청함으로써 반환되는 콘텐츠를 예측할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="c8559-114">안전하지 않은 요청을 보안 엔드포인트로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="c8559-115">외부 사이트에서 자산을 자체의 콘텐츠에 연결하여 다른 사이트에서 호스팅된 정적 자산을 사용하는 핫 링크를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="c8559-116">URL 재작성은 응용 프로그램의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="c8559-117">가능한 경우 규칙의 수와 복잡성을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="c8559-118">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c8559-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="c8559-119">URL 리디렉션 및 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="c8559-120">*URL 리디렉션* 과 *URL 재작성* 간의 표현 차이는 미묘하지만 클라이언트에 리소스를 제공하는 데 더 중요한 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="c8559-121">ASP.NET Core의 URL 재작성 미들웨어는 두 가지 모두에 대한 요구를 만족합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="c8559-122">*URL 리디렉션* 은 클라이언트 쪽 작업과 관련되어 있습니다. 여기서 클라이언트는 원래 요청한 클라이언트와는 다른 주소로 리소스에 액세스하도록 지시받습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="c8559-123">이 경우 서버를 왕복해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-123">This requires a round trip to the server.</span></span> <span data-ttu-id="c8559-124">클라이언트로 반환된 리디렉션 URL은 클라이언트가 리소스에 대한 새로운 요청을 만들 때 브라우저의 주소 표시줄에 나타나게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="c8559-125">`/resource`가 `/different-resource`로 *리디렉션* 되는 경우 서버는 임시 또는 영구 리디렉션을 나타내는 상태 코드와 함께 클라이언트가 `/different-resource`에서 리소스를 가져와야 한다고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 임시 변경됩니다.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="c8559-131">요청을 다른 URL로 리디렉션하는 경우 응답과 함께 상태 코드를 지정하여 영구 리디렉션 또는 임시 리디렉션인지의 여부를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="c8559-132">*301 - 영구적으로 이동됨* 상태 코드는 리소스에 새 영구 URL이 있고, 리소스에 대한 이후의 모든 요청에서 새 URL을 사용해야 한다고 클라이언트에 지시하려는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="c8559-133">*301 상태 코드를 받으면 클라이언트에서 응답을 캐시하고 다시 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="c8559-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="c8559-134">*302 - 있음* 상태 코드는 리디렉션이 일시적이거나 일반적으로 변경될 수 있는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="c8559-135">302 상태 코드는 클라이언트에서 URL을 저장하지 않고 나중에 사용하지 못하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="c8559-136">상태 코드에 대한 자세한 내용은 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c8559-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="c8559-137">*URL 재작성* 은 요청한 클라이언트와 다른 리소스 주소에서 리소스를 제공하는 서버 쪽 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="c8559-138">URL을 다시 작성하는 경우 서버를 왕복할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="c8559-139">다시 작성된 URL은 클라이언트에 반환되지 않고 브라우저의 주소 표시줄에도 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="c8559-140">`/resource`가 `/different-resource`에 *다시 작성* 되면 서버에서 *내부적으로*`/different-resource`에 있는 리소스를 가져와서 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="c8559-141">클라이언트는 다시 작성된 URL에서 리소스를 검색할 수 있지만, 요청을 만들고 응답을 받을 때 리소스가 다시 작성된 URL에 있음을 클라이언트에 알리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 변경됩니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="c8559-146">URL 재작성 예제 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="c8559-146">URL rewriting sample app</span></span>

<span data-ttu-id="c8559-147">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/)을 사용하면 URL 재작성 미들웨어의 기능을 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="c8559-148">이 앱은 리디렉션 및 재작성 규칙을 적용하고, 여러 시나리오에 대해 리디렉션되거나 다시 작성된 URL을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="c8559-149">URL 재작성 미들웨어를 사용해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="c8559-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="c8559-150">다음 방법을 사용할 수 없는 경우 URL 재작성 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* <span data-ttu-id="c8559-151">Windows Server의 IIS를 사용하는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)</span><span class="sxs-lookup"><span data-stu-id="c8559-151">[URL Rewrite module with IIS on Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)</span></span>
* <span data-ttu-id="c8559-152">Apache Server의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/)</span><span class="sxs-lookup"><span data-stu-id="c8559-152">[Apache mod_rewrite module on Apache Server](https://httpd.apache.org/docs/2.4/rewrite/)</span></span>
* [<span data-ttu-id="c8559-153">Nginx의 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="c8559-154">또한 앱이 [HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)에서 호스팅되는 경우 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="c8559-155">IIS, Apache 및 Nginx에서 서버 기반 URL 재작성 기술을 사용하는 주요 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="c8559-156">미들웨어에서 이러한 모듈의 전체 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="c8559-157">서버 모듈의 일부 기능이 IIS 재작성 모듈의 `IsFile` 및 `IsDirectory` 제약 조건과 같은 ASP.NET Core 프로젝트에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="c8559-158">바로 이런 시나리오에서 대신 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="c8559-159">미들웨어의 성능이 아마도 모듈의 성능과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="c8559-160">벤치마킹은 성능을 가장 많이 저하시키는 방법 또는 저하된 성능을 무시할 수 있는 경우를 확인할 수 있는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="c8559-161">패키지</span><span class="sxs-lookup"><span data-stu-id="c8559-161">Package</span></span>

<span data-ttu-id="c8559-162">URL 다시 작성 미들웨어는 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 패키지에서 제공하며, 이 패키지는 ASP.NET Core 앱에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="c8559-163">확장 및 옵션</span><span class="sxs-lookup"><span data-stu-id="c8559-163">Extension and options</span></span>

<span data-ttu-id="c8559-164">각각의 재작성 규칙에 대한 확장 메서드를 사용하여 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 클래스의 인스턴스를 만들어 URL 재작성 및 리디렉션 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="c8559-165">처리하고자 하는 순서대로 여러 규칙을 연결하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="c8559-166">`RewriteOptions`는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>를 사용하여 요청 파이프라인에 추가될 때 URL 재작성 미들웨어에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="c8559-167">www 이외 요청을 www로 리디렉션</span><span class="sxs-lookup"><span data-stu-id="c8559-167">Redirect non-www to www</span></span>

<span data-ttu-id="c8559-168">이러한 옵션은 앱이 `www` 이외 요청을 `www`로 리디렉션하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="c8559-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: 요청이 `www`가 아닌 경우 영구적으로 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="c8559-170">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="c8559-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: 들어오는 요청이 `www`가 아닌 경우 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="c8559-172">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="c8559-173">오버로드를 사용하면 응답에 대한 상태 코드를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="c8559-174">상태 코드 할당을 위해 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스의 필드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="c8559-175">URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="c8559-175">URL redirect</span></span>

<span data-ttu-id="c8559-176">요청을 리디렉션하려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="c8559-177">첫 번째 매개 변수에는 들어오는 URL의 경로와 일치하는 부분을 찾기 위한 정규식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="c8559-178">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="c8559-179">필요한 경우 세 번째 매개 변수로 상태 코드를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="c8559-180">상태 코드를 지정하지 않으면 상태 코드가 기본적으로 *302 - 있음* 으로 설정되며, 이는 리소스가 일시적으로 이동하거나 대체되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="c8559-181">개발자 도구가 활성화된 브라우저에서 예제 응용 프로그램에 `/redirect-rule/1234/5678` 경로를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="c8559-182">그러면 정규식이 `redirect-rule/(.*)`의 요청 경로와 일치하므로 `/redirected/1234/5678`로 경로가 치환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="c8559-183">리디렉션 URL은 *302 - 있음* 상태 코드와 함께 클라이언트로 다시 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="c8559-184">브라우저는 리디렉션 URL에 대한 새로운 요청을 만들고 이 주소는 브라우저의 주소 표시줄에 출력됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="c8559-185">샘플 앱의 규칙이 리디렉션 URL에서 일치하지 않으므로 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="c8559-186">두 번째 요청에서 앱의 *200 - 정상* 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="c8559-187">응답 본문에 리디렉션 URL이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="c8559-188">URL이 *리디렉션* 되면 서버로의 왕복이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="c8559-189">리디렉션 규칙을 설정할 때에는 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="c8559-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="c8559-190">리디렉션 규칙은 리디렉션 이후를 포함하여 앱에 대한 모든 요청에서 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="c8559-191">따라서 *무한 리디렉션 루프* 를 실수로 만들기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="c8559-192">원본 요청: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c8559-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="c8559-194">표현식에서 괄호로 둘러쌓인 부분을 *캡처 그룹(Capture Group)* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="c8559-195">그리고 표현식에서 마침표(`.`)는 모든 문자와 일치함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="c8559-196">마지막으로 별표(`*`)는 앞의 문자와 0번 이상 일치함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="c8559-197">따라서 URL의 마지막 두 세그먼트, `1234/5678`은 캡쳐 그룹 `(.*)`에 의해 캡쳐됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="c8559-198">즉 요청 URL에서 `redirect-rule/` 이후에 제공하는 모든 값이 이 단일 캡처 그룹에 의해서 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="c8559-199">대체 문자열에서, 캡처된 그룹은 캡처의 일련번호가 뒤에 붙는 달러 기호(`$`)를 통해서 문자열에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="c8559-200">첫 번째 캡처 그룹 값은 `$1`로 얻을 수 있고, 두 번째 캡처 그룹 값은 `$2`로 얻을 수 있으며, 이는 정규식에 포함된 캡처 그룹에 대해 순차적으로 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="c8559-201">예제 응용 프로그램에서 리디렉션 규칙의 정규식에 캡처된 그룹은 단 하나뿐이므로 대체 문자열에 삽입되는 그룹도 `$1` 하나뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="c8559-202">규칙이 적용되고 나면 URL은 `/redirected/1234/5678`로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="c8559-203">보안 엔드포인트에 대한 URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="c8559-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="c8559-204"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>를 사용하여 HTTPS 프로토콜을 통해 HTTP 요청을 동일한 호스트 및 경로로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="c8559-205">상태 코드가 제공되지 않는 경우 미들웨어는 기본적으로 *302 - 있음* 으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="c8559-206">포트가 제공되지 않는 경우 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="c8559-207">미들웨어가 기본적으로 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="c8559-208">체계가 `https`(HTTPS 프로토콜)로 변경되고 클라이언트에서 443 포트의 리소스에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="c8559-209">다음 예제에서는 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정하고 포트를 5001로 변경하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="c8559-210"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>를 사용하여 443 포트의 HTTPS 프로토콜을 통해 안전하지 않은 요청을 동일한 호스트 및 경로로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="c8559-211">미들웨어에서 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="c8559-212">추가 리디렉션 규칙을 요구하지 않고 보안 엔드포인트로 리디렉션하는 경우 HTTPS 리디렉션 미들웨어를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="c8559-213">자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl#require-https) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c8559-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="c8559-214">예제 응용 프로그램을 통해서 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`의 사용 방법을 확인해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="c8559-215">먼저 `RewriteOptions`에 이 확장 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="c8559-216">모든 URL에서 앱에 대한 안전하지 않은 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="c8559-217">자체 서명된 인증서를 신뢰할 수 없다는 브라우저 보안 경고는 무시하면 됩니다. 또는 인증서를 신뢰할 예외를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="c8559-218">`AddRedirectToHttps(301, 5001)`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c8559-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="c8559-220">`AddRedirectToHttpsPermanent`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c8559-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="c8559-222">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-222">URL rewrite</span></span>

<span data-ttu-id="c8559-223">URL을 재작성하는 규칙을 만들려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="c8559-224">첫 번째 매개 변수에는 들어오는 URL의 경로에서 일치하는 정규식이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="c8559-225">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="c8559-226">세 번째 매개 변수, `skipRemainingRules: {true|false}`는 현재 규칙이 적용되는 경우에 추가 재작성 규칙을 건너뛸 것인지 여부를 미들웨어에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="c8559-227">원래 요청: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c8559-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="c8559-229">식의 시작 부분에 있는 캐럿(`^`)은 URL 경로의 시작 부분에서 일치가 시작된다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="c8559-230">`redirect-rule/(.*)` 리디렉션 규칙이 있는 이전 예제에는 정규식의 시작 부분에 캐럿(`^`)이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="c8559-231">따라서 일치하는 모든 문자가 경로의 `redirect-rule/` 앞에 나올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="c8559-232">경로</span><span class="sxs-lookup"><span data-stu-id="c8559-232">Path</span></span>                               | <span data-ttu-id="c8559-233">일치</span><span class="sxs-lookup"><span data-stu-id="c8559-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="c8559-234">예</span><span class="sxs-lookup"><span data-stu-id="c8559-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="c8559-235">예</span><span class="sxs-lookup"><span data-stu-id="c8559-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="c8559-236">예</span><span class="sxs-lookup"><span data-stu-id="c8559-236">Yes</span></span>   |

<span data-ttu-id="c8559-237">반면 `^rewrite-rule/(\d+)/(\d+)` 재작성 규칙의 경우에는 오로지 `rewrite-rule/`로 시작하는 경로만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="c8559-238">다음 표에는 일치에서의 차이가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="c8559-239">경로</span><span class="sxs-lookup"><span data-stu-id="c8559-239">Path</span></span>                              | <span data-ttu-id="c8559-240">일치</span><span class="sxs-lookup"><span data-stu-id="c8559-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="c8559-241">예</span><span class="sxs-lookup"><span data-stu-id="c8559-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="c8559-242">아니요</span><span class="sxs-lookup"><span data-stu-id="c8559-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="c8559-243">아니요</span><span class="sxs-lookup"><span data-stu-id="c8559-243">No</span></span>    |

<span data-ttu-id="c8559-244">표현식의 `^rewrite-rule/` 부분 뒤에는 계속해서 두 개의 캡처 그룹, `(\d+)/(\d+)`이 위치해 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="c8559-245">여기서 `\d`는 *숫자 하나와 일치* 함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="c8559-246">그리고 더하기 기호(`+`)는 *앞의 문자와 한 번 이상 일치* 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="c8559-247">따라서 URL은 반드시 숫자 뒤에 슬래시와 다른 숫자가 연이어 나타나는 부분을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="c8559-248">이 캡쳐 그룹들은 `$1` 및 `$2`를 통해서 재작성 URL에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="c8559-249">재작성 규칙의 대체 문자열은 캡처된 그룹을 쿼리 문자열에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="c8559-250">즉, 요청 경로 `/rewrite-rule/1234/5678`은 `/rewritten?var1=1234&var2=5678`에서 리소스를 가져오도록 재작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="c8559-251">원본 요청에 쿼리 문자열이 있으면 URL을 다시 작성할 때 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="c8559-252">리소스를 가져오기 위해 서버를 왕복하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="c8559-253">리소스가 있으면 이를 가져와서 *200 - 정상* 상태 코드와 함께 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="c8559-254">클라이언트는 리디렉션 되지 않으므로 브라우저 주소 표시줄의 URL은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="c8559-255">클라이언트는 서버에서 URL 재작성 작업이 발생했음을 검색할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c8559-256">일치 규칙은 컴퓨팅 측면에서 비용이 많이 들고 앱의 응답 속도가 증가되므로 가능한 경우 `skipRemainingRules: true`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="c8559-257">최대한 빠른 응용 프로그램 응답을 위해서는:</span><span class="sxs-lookup"><span data-stu-id="c8559-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="c8559-258">재작성 규칙을 가장 자주 일치하는 규칙에서 가장 드물게 일치하는 규칙으로의 순서로 정렬합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="c8559-259">일치가 발생하고 추가적인 규칙 처리가 필요하지 않다면 나머지 규칙의 처리를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="c8559-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="c8559-260">Apache mod_rewrite</span></span>

<span data-ttu-id="c8559-261"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>를 사용하면 Apache mod_rewrite 규칙을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="c8559-262">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c8559-263">mod_rewrite 규칙에 대한 보다 자세한 내용과 예제는 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="c8559-264"><xref:System.IO.StreamReader>는 *ApacheModRewrite.txt* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="c8559-265">예제 응용 프로그램은 `/apache-mod-rules-redirect/(.\*)`에서 `/redirected?id=$1`로 요청을 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="c8559-266">응답 상태 코드는 *302 - 있음* 입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="c8559-267">원본 요청: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="c8559-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="c8559-269">미들웨어는 다음과 같은 Apache mod_rewrite 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="c8559-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="c8559-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="c8559-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c8559-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c8559-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c8559-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c8559-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c8559-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="c8559-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="c8559-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="c8559-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c8559-275">HTTP_HOST</span></span>
* <span data-ttu-id="c8559-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c8559-276">HTTP_REFERER</span></span>
* <span data-ttu-id="c8559-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c8559-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c8559-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c8559-278">HTTPS</span></span>
* <span data-ttu-id="c8559-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="c8559-279">IPV6</span></span>
* <span data-ttu-id="c8559-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c8559-280">QUERY_STRING</span></span>
* <span data-ttu-id="c8559-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="c8559-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c8559-282">REMOTE_PORT</span></span>
* <span data-ttu-id="c8559-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c8559-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c8559-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="c8559-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="c8559-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="c8559-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="c8559-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c8559-286">REQUEST_URI</span></span>
* <span data-ttu-id="c8559-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c8559-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="c8559-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-288">SERVER_ADDR</span></span>
* <span data-ttu-id="c8559-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="c8559-289">SERVER_PORT</span></span>
* <span data-ttu-id="c8559-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="c8559-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="c8559-291">TIME</span><span class="sxs-lookup"><span data-stu-id="c8559-291">TIME</span></span>
* <span data-ttu-id="c8559-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="c8559-292">TIME_DAY</span></span>
* <span data-ttu-id="c8559-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="c8559-293">TIME_HOUR</span></span>
* <span data-ttu-id="c8559-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="c8559-294">TIME_MIN</span></span>
* <span data-ttu-id="c8559-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="c8559-295">TIME_MON</span></span>
* <span data-ttu-id="c8559-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="c8559-296">TIME_SEC</span></span>
* <span data-ttu-id="c8559-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="c8559-297">TIME_WDAY</span></span>
* <span data-ttu-id="c8559-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="c8559-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="c8559-299">IIS URL 재작성 모듈 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="c8559-300">IIS URL 재작성 모듈에 적용되는 것과 동일한 규칙 세트를 사용하려면 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="c8559-301">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c8559-302">Windows Server IIS에서 실행하는 경우 미들웨어에서 앱의 *web.config* 파일을 사용하도록 지시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="c8559-303">IIS를 사용하는 경우 IIS 재작성 모듈과 충돌하지 않도록 이러한 규칙을 앱의 *web.config* 파일 외부에 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="c8559-304">IIS URL 재작성 모듈 규칙에 대한 보다 자세한 내용 및 예제는 [URL 재작성 모듈 2.0 사용](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)과 [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="c8559-305"><xref:System.IO.StreamReader>는 *IISUrlRewrite.xml* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="c8559-306">예제 응용 프로그램은 `/iis-rules-rewrite/(.*)`에서 `/rewritten?id=$1`로 요청을 재작성합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="c8559-307">응답이 *200 - 정상* 상태 코드와 함께 클라이언트에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="c8559-308">원본 요청: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="c8559-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="c8559-310">원하지 않는 방식으로 응용 프로그램에 영향을 주는 서버 수준 규칙이 구성된 활성 IIS 재작성 모듈이 존재할 경우 응용 프로그램에 대한 IIS 재작성 모듈을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="c8559-311">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="c8559-312">지원되지 않는 기능</span><span class="sxs-lookup"><span data-stu-id="c8559-312">Unsupported features</span></span>

<span data-ttu-id="c8559-313">미들웨어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원하지 않습니다:</span><span class="sxs-lookup"><span data-stu-id="c8559-313">The middleware doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="c8559-314">아웃바운드 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-314">Outbound Rules</span></span>
* <span data-ttu-id="c8559-315">사용자 지정 서버 변수</span><span class="sxs-lookup"><span data-stu-id="c8559-315">Custom Server Variables</span></span>
* <span data-ttu-id="c8559-316">와일드카드</span><span class="sxs-lookup"><span data-stu-id="c8559-316">Wildcards</span></span>
* <span data-ttu-id="c8559-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="c8559-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="c8559-318">지원되는 서버 변수</span><span class="sxs-lookup"><span data-stu-id="c8559-318">Supported server variables</span></span>

<span data-ttu-id="c8559-319">미들웨어는 다음과 같은 IIS URL 재작성 모듈 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="c8559-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="c8559-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="c8559-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="c8559-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="c8559-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="c8559-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c8559-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c8559-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c8559-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c8559-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c8559-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="c8559-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c8559-325">HTTP_HOST</span></span>
* <span data-ttu-id="c8559-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c8559-326">HTTP_REFERER</span></span>
* <span data-ttu-id="c8559-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="c8559-327">HTTP_URL</span></span>
* <span data-ttu-id="c8559-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c8559-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c8559-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c8559-329">HTTPS</span></span>
* <span data-ttu-id="c8559-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="c8559-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c8559-331">QUERY_STRING</span></span>
* <span data-ttu-id="c8559-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="c8559-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c8559-333">REMOTE_PORT</span></span>
* <span data-ttu-id="c8559-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c8559-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c8559-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c8559-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="c8559-336"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>를 이용해서 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져올 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="c8559-337">이 방식이 재작성 규칙 파일의 위치에 대해 더 많은 유연성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="c8559-338">재작성 규칙 파일이 서버의 지정한 경로에 배포되는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="c8559-339">메서드 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-339">Method-based rule</span></span>

<span data-ttu-id="c8559-340">메서드를 이용해서 직접 규칙 로직을 구현하고 싶다면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="c8559-341">`Add`는 메서드에서 사용할 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용할 수 있게 하는 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="c8559-342">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)는 추가 파이프라인 처리가 수행되는 방법을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="c8559-343">값을 다음 표에 설명된 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 필드 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="c8559-344">컨텍스트 결과 다시 작성</span><span class="sxs-lookup"><span data-stu-id="c8559-344">Rewrite context result</span></span>               | <span data-ttu-id="c8559-345">작업</span><span class="sxs-lookup"><span data-stu-id="c8559-345">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="c8559-346">`RuleResult.ContinueRules`(기본값)</span><span class="sxs-lookup"><span data-stu-id="c8559-346">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="c8559-347">규칙 적용을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-347">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="c8559-348">규칙 적용을 중지하고 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-348">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="c8559-349">규칙 적용을 중지하고 컨텍스트를 다음 미들웨어로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-349">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="c8559-350">예제 응용 프로그램은 *.xml* 로 끝나는 경로 요청을 리디렉션하는 메서드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-350">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="c8559-351">`/file.xml`에 대한 요청이 수행되면 해당 요청이 `/xmlfiles/file.xml`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-351">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="c8559-352">상태 코드는 *301 - 영구적으로 이동됨* 으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-352">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="c8559-353">브라우저에서 */xmlfiles/file.xml* 에 대한 새 요청이 수행되면 정적 파일 미들웨어에서 *wwwroot/xmlfiles* 폴더의 파일을 클라이언트에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-353">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="c8559-354">리디렉션의 경우 응답의 상태 코드를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-354">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="c8559-355">그렇지 않으면 *200 - 정상* 상태 코드가 반환되고 클라이언트에서 리디렉션이 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-355">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="c8559-356">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8559-356">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="c8559-357">또한 이 방법은 요청을 다시 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-357">This approach can also rewrite requests.</span></span> <span data-ttu-id="c8559-358">샘플 앱에서는 *wwwroot* 폴더의 *file.txt* 텍스트 파일을 제공할 텍스트 파일 요청의 경로를 다시 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-358">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="c8559-359">정적 파일 미들웨어는 업데이트된 요청 경로에 따라 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-359">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="c8559-360">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8559-360">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="c8559-361">IRule 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-361">IRule-based rule</span></span>

<span data-ttu-id="c8559-362"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하여 <xref:Microsoft.AspNetCore.Rewrite.IRule> 인터페이스를 구현하는 클래스에서 규칙 논리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-362">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="c8559-363">`IRule`은 메서드 기반 규칙 방식을 사용하는 것보다 더 높은 유연성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-363">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="c8559-364">구현 클래스에는 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 메서드에 대한 매개 변수를 전달할 수 있는 생성자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-364">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="c8559-365">예제 응용 프로그램은 `extension` 및 `newPath` 매개 변수 값들이 다양한 조건을 만족하는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-365">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="c8559-366">`extension`매개 변수는 값을 포함하고 있어야 하고, 그 값은 *.png*, *.jpg*, 또는 *.gif* 중 하나이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-366">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="c8559-367">만약 `newPath`가 유효하지 않으면 <xref:System.ArgumentException>이 던져집니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-367">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="c8559-368">*image.png* 에 대한 요청이 수행되면 해당 요청이 `/png-images/image.png`으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-368">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="c8559-369">*image.jpg* 에 대한 요청이 수행되면 해당 요청이 `/jpg-images/image.jpg`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-369">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="c8559-370">상태 코드는 *301 - 영구적으로 이동됨* 으로 설정되고, `context.Result`는 규칙 처리를 중지하고 응답을 보내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-370">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="c8559-371">원본 요청: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="c8559-371">Original Request: `/image.png`</span></span>

![image.png에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="c8559-373">원본 요청: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="c8559-373">Original Request: `/image.jpg`</span></span>

![image.jpg에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="c8559-375">정규식 예제</span><span class="sxs-lookup"><span data-stu-id="c8559-375">Regex examples</span></span>

| <span data-ttu-id="c8559-376">Goal</span><span class="sxs-lookup"><span data-stu-id="c8559-376">Goal</span></span> | <span data-ttu-id="c8559-377">정규식 문자열 및</span><span class="sxs-lookup"><span data-stu-id="c8559-377">Regex String &</span></span><br><span data-ttu-id="c8559-378">일치 예제</span><span class="sxs-lookup"><span data-stu-id="c8559-378">Match Example</span></span> | <span data-ttu-id="c8559-379">대체 문자열 및</span><span class="sxs-lookup"><span data-stu-id="c8559-379">Replacement String &</span></span><br><span data-ttu-id="c8559-380">출력 예제</span><span class="sxs-lookup"><span data-stu-id="c8559-380">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="c8559-381">경로를 쿼리 문자열로 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-381">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="c8559-382">후행 슬래시 제거</span><span class="sxs-lookup"><span data-stu-id="c8559-382">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="c8559-383">후행 슬래시 적용</span><span class="sxs-lookup"><span data-stu-id="c8559-383">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="c8559-384">특정 요청 재작성 방지</span><span class="sxs-lookup"><span data-stu-id="c8559-384">Avoid rewriting specific requests</span></span> | <span data-ttu-id="c8559-385">`^(.*)(?<!\.axd)$` 또는 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="c8559-385">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="c8559-386">예: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="c8559-386">Yes: `/resource.htm`</span></span><br><span data-ttu-id="c8559-387">아니요: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="c8559-387">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="c8559-388">URL 세그먼트 재정렬</span><span class="sxs-lookup"><span data-stu-id="c8559-388">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="c8559-389">URL 세그먼트 대체</span><span class="sxs-lookup"><span data-stu-id="c8559-389">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c8559-390">본문에서는 ASP.NET Core 응용 프로그램에서 URL 재작성 미들웨어를 사용하는 방법에 관한 지침과 URL 재작성에 관해서 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-390">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="c8559-391">URL 재작성은 하나 이상의 미리 정의된 규칙을 기반으로 하는 요청 URL을 수정하는 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-391">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="c8559-392">URL 재작성은 위치와 주소가 밀접하게 연결되지 않도록 리소스 위치와 해당 주소 간의 추상화를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-392">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="c8559-393">URL 재작성이 중요한 몇 가지 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-393">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="c8559-394">서버 리소스를 일시적 또는 영구적으로 이동하거나 대체하고, 해당 리소스에 대한 안정적인 로케이터를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-394">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="c8559-395">여러 앱 또는 한 앱의 여러 영역 간에 요청 처리를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-395">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="c8559-396">들어오는 요청의 URL 세그먼트를 제거, 추가, 또는 다시 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-396">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="c8559-397">SEO(검색 엔진 최적화)에 맞게 공용 URL을 최적화합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-397">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="c8559-398">친숙한 공용 URL을 사용하여 방문자가 리소스를 요청함으로써 반환되는 콘텐츠를 예측할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-398">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="c8559-399">안전하지 않은 요청을 보안 엔드포인트로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-399">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="c8559-400">외부 사이트에서 자산을 자체의 콘텐츠에 연결하여 다른 사이트에서 호스팅된 정적 자산을 사용하는 핫 링크를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-400">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="c8559-401">URL 재작성은 응용 프로그램의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-401">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="c8559-402">가능한 경우 규칙의 수와 복잡성을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-402">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="c8559-403">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c8559-403">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="c8559-404">URL 리디렉션 및 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-404">URL redirect and URL rewrite</span></span>

<span data-ttu-id="c8559-405">*URL 리디렉션* 과 *URL 재작성* 간의 표현 차이는 미묘하지만 클라이언트에 리소스를 제공하는 데 더 중요한 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-405">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="c8559-406">ASP.NET Core의 URL 재작성 미들웨어는 두 가지 모두에 대한 요구를 만족합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-406">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="c8559-407">*URL 리디렉션* 은 클라이언트 쪽 작업과 관련되어 있습니다. 여기서 클라이언트는 원래 요청한 클라이언트와는 다른 주소로 리소스에 액세스하도록 지시받습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-407">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="c8559-408">이 경우 서버를 왕복해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-408">This requires a round trip to the server.</span></span> <span data-ttu-id="c8559-409">클라이언트로 반환된 리디렉션 URL은 클라이언트가 리소스에 대한 새로운 요청을 만들 때 브라우저의 주소 표시줄에 나타나게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-409">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="c8559-410">`/resource`가 `/different-resource`로 *리디렉션* 되는 경우 서버는 임시 또는 영구 리디렉션을 나타내는 상태 코드와 함께 클라이언트가 `/different-resource`에서 리소스를 가져와야 한다고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-410">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 임시 변경됩니다.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="c8559-416">요청을 다른 URL로 리디렉션하는 경우 응답과 함께 상태 코드를 지정하여 영구 리디렉션 또는 임시 리디렉션인지의 여부를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-416">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="c8559-417">*301 - 영구적으로 이동됨* 상태 코드는 리소스에 새 영구 URL이 있고, 리소스에 대한 이후의 모든 요청에서 새 URL을 사용해야 한다고 클라이언트에 지시하려는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-417">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="c8559-418">*301 상태 코드를 받으면 클라이언트에서 응답을 캐시하고 다시 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="c8559-418">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="c8559-419">*302 - 있음* 상태 코드는 리디렉션이 일시적이거나 일반적으로 변경될 수 있는 경우에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-419">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="c8559-420">302 상태 코드는 클라이언트에서 URL을 저장하지 않고 나중에 사용하지 못하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-420">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="c8559-421">상태 코드에 대한 자세한 내용은 [RFC 2616: 상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c8559-421">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="c8559-422">*URL 재작성* 은 요청한 클라이언트와 다른 리소스 주소에서 리소스를 제공하는 서버 쪽 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-422">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="c8559-423">URL을 다시 작성하는 경우 서버를 왕복할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-423">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="c8559-424">다시 작성된 URL은 클라이언트에 반환되지 않고 브라우저의 주소 표시줄에도 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-424">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="c8559-425">`/resource`가 `/different-resource`에 *다시 작성* 되면 서버에서 *내부적으로*`/different-resource`에 있는 리소스를 가져와서 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-425">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="c8559-426">클라이언트는 다시 작성된 URL에서 리소스를 검색할 수 있지만, 요청을 만들고 응답을 받을 때 리소스가 다시 작성된 URL에 있음을 클라이언트에 알리지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-426">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI 서비스 엔드포인트는 서버 측에서 버전 1(v1)에서 버전 2(v2)로 변경됩니다.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="c8559-431">URL 재작성 예제 응용 프로그램</span><span class="sxs-lookup"><span data-stu-id="c8559-431">URL rewriting sample app</span></span>

<span data-ttu-id="c8559-432">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/)을 사용하면 URL 재작성 미들웨어의 기능을 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-432">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="c8559-433">이 앱은 리디렉션 및 재작성 규칙을 적용하고, 여러 시나리오에 대해 리디렉션되거나 다시 작성된 URL을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-433">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="c8559-434">URL 재작성 미들웨어를 사용해야 하는 경우</span><span class="sxs-lookup"><span data-stu-id="c8559-434">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="c8559-435">다음 방법을 사용할 수 없는 경우 URL 재작성 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-435">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* <span data-ttu-id="c8559-436">Windows Server의 IIS를 사용하는 [URL 재작성 모듈](https://www.iis.net/downloads/microsoft/url-rewrite)</span><span class="sxs-lookup"><span data-stu-id="c8559-436">[URL Rewrite module with IIS on Windows Server](https://www.iis.net/downloads/microsoft/url-rewrite)</span></span>
* <span data-ttu-id="c8559-437">Apache Server의 [Apache mod_rewrite 모듈](https://httpd.apache.org/docs/2.4/rewrite/)</span><span class="sxs-lookup"><span data-stu-id="c8559-437">[Apache mod_rewrite module on Apache Server](https://httpd.apache.org/docs/2.4/rewrite/)</span></span>
* [<span data-ttu-id="c8559-438">Nginx의 URL 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-438">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="c8559-439">또한 앱이 [HTTP.sys 서버](xref:fundamentals/servers/httpsys)(이전의 WebListener)에서 호스팅되는 경우 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-439">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="c8559-440">IIS, Apache 및 Nginx에서 서버 기반 URL 재작성 기술을 사용하는 주요 이유는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-440">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="c8559-441">미들웨어에서 이러한 모듈의 전체 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-441">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="c8559-442">서버 모듈의 일부 기능이 IIS 재작성 모듈의 `IsFile` 및 `IsDirectory` 제약 조건과 같은 ASP.NET Core 프로젝트에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-442">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="c8559-443">바로 이런 시나리오에서 대신 미들웨어를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-443">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="c8559-444">미들웨어의 성능이 아마도 모듈의 성능과 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-444">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="c8559-445">벤치마킹은 성능을 가장 많이 저하시키는 방법 또는 저하된 성능을 무시할 수 있는 경우를 확인할 수 있는 유일한 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-445">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="c8559-446">패키지</span><span class="sxs-lookup"><span data-stu-id="c8559-446">Package</span></span>

<span data-ttu-id="c8559-447">프로젝트에 미들웨어를 포함시키려면 [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) 패키지가 포함된 프로젝트 파일의 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-447">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="c8559-448">`Microsoft.AspNetCore.App` 메타패키지를 사용하지 않는 경우 `Microsoft.AspNetCore.Rewrite` 패키지에 프로젝트 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-448">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="c8559-449">확장 및 옵션</span><span class="sxs-lookup"><span data-stu-id="c8559-449">Extension and options</span></span>

<span data-ttu-id="c8559-450">각각의 재작성 규칙에 대한 확장 메서드를 사용하여 [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) 클래스의 인스턴스를 만들어 URL 재작성 및 리디렉션 규칙을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-450">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="c8559-451">처리하고자 하는 순서대로 여러 규칙을 연결하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-451">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="c8559-452">`RewriteOptions`는 <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>를 사용하여 요청 파이프라인에 추가될 때 URL 재작성 미들웨어에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-452">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="c8559-453">www 이외 요청을 www로 리디렉션</span><span class="sxs-lookup"><span data-stu-id="c8559-453">Redirect non-www to www</span></span>

<span data-ttu-id="c8559-454">이러한 옵션은 앱이 `www` 이외 요청을 `www`로 리디렉션하도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-454">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="c8559-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: 요청이 `www`가 아닌 경우 영구적으로 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="c8559-456">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-456">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="c8559-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: 들어오는 요청이 `www`가 아닌 경우 요청을 `www` 하위 도메인으로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="c8559-458">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 상태 코드를 사용하여 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-458">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="c8559-459">오버로드를 사용하면 응답에 대한 상태 코드를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-459">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="c8559-460">상태 코드 할당을 위해 <xref:Microsoft.AspNetCore.Http.StatusCodes> 클래스의 필드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-460">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="c8559-461">URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="c8559-461">URL redirect</span></span>

<span data-ttu-id="c8559-462">요청을 리디렉션하려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-462">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="c8559-463">첫 번째 매개 변수에는 들어오는 URL의 경로와 일치하는 부분을 찾기 위한 정규식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-463">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="c8559-464">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-464">The second parameter is the replacement string.</span></span> <span data-ttu-id="c8559-465">필요한 경우 세 번째 매개 변수로 상태 코드를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-465">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="c8559-466">상태 코드를 지정하지 않으면 상태 코드가 기본적으로 *302 - 있음* 으로 설정되며, 이는 리소스가 일시적으로 이동하거나 대체되었음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-466">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="c8559-467">개발자 도구가 활성화된 브라우저에서 예제 응용 프로그램에 `/redirect-rule/1234/5678` 경로를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-467">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="c8559-468">그러면 정규식이 `redirect-rule/(.*)`의 요청 경로와 일치하므로 `/redirected/1234/5678`로 경로가 치환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-468">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="c8559-469">리디렉션 URL은 *302 - 있음* 상태 코드와 함께 클라이언트로 다시 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-469">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="c8559-470">브라우저는 리디렉션 URL에 대한 새로운 요청을 만들고 이 주소는 브라우저의 주소 표시줄에 출력됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-470">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="c8559-471">샘플 앱의 규칙이 리디렉션 URL에서 일치하지 않으므로 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-471">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="c8559-472">두 번째 요청에서 앱의 *200 - 정상* 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-472">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="c8559-473">응답 본문에 리디렉션 URL이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-473">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="c8559-474">URL이 *리디렉션* 되면 서버로의 왕복이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-474">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="c8559-475">리디렉션 규칙을 설정할 때에는 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="c8559-475">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="c8559-476">리디렉션 규칙은 리디렉션 이후를 포함하여 앱에 대한 모든 요청에서 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-476">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="c8559-477">따라서 *무한 리디렉션 루프* 를 실수로 만들기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-477">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="c8559-478">원본 요청: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c8559-478">Original Request: `/redirect-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="c8559-480">표현식에서 괄호로 둘러쌓인 부분을 *캡처 그룹(Capture Group)* 이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-480">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="c8559-481">그리고 표현식에서 마침표(`.`)는 모든 문자와 일치함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-481">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="c8559-482">마지막으로 별표(`*`)는 앞의 문자와 0번 이상 일치함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-482">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="c8559-483">따라서 URL의 마지막 두 세그먼트, `1234/5678`은 캡쳐 그룹 `(.*)`에 의해 캡쳐됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-483">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="c8559-484">즉 요청 URL에서 `redirect-rule/` 이후에 제공하는 모든 값이 이 단일 캡처 그룹에 의해서 캡처됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-484">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="c8559-485">대체 문자열에서, 캡처된 그룹은 캡처의 일련번호가 뒤에 붙는 달러 기호(`$`)를 통해서 문자열에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-485">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="c8559-486">첫 번째 캡처 그룹 값은 `$1`로 얻을 수 있고, 두 번째 캡처 그룹 값은 `$2`로 얻을 수 있으며, 이는 정규식에 포함된 캡처 그룹에 대해 순차적으로 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-486">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="c8559-487">예제 응용 프로그램에서 리디렉션 규칙의 정규식에 캡처된 그룹은 단 하나뿐이므로 대체 문자열에 삽입되는 그룹도 `$1` 하나뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-487">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="c8559-488">규칙이 적용되고 나면 URL은 `/redirected/1234/5678`로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-488">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="c8559-489">보안 엔드포인트에 대한 URL 리디렉션</span><span class="sxs-lookup"><span data-stu-id="c8559-489">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="c8559-490"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>를 사용하여 HTTPS 프로토콜을 통해 HTTP 요청을 동일한 호스트 및 경로로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-490">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="c8559-491">상태 코드가 제공되지 않는 경우 미들웨어는 기본적으로 *302 - 있음* 으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-491">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="c8559-492">포트가 제공되지 않는 경우 다음과 같이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-492">If the port isn't supplied:</span></span>

* <span data-ttu-id="c8559-493">미들웨어가 기본적으로 `null`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-493">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="c8559-494">체계가 `https`(HTTPS 프로토콜)로 변경되고 클라이언트에서 443 포트의 리소스에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-494">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="c8559-495">다음 예제에서는 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정하고 포트를 5001로 변경하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-495">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="c8559-496"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>를 사용하여 443 포트의 HTTPS 프로토콜을 통해 안전하지 않은 요청을 동일한 호스트 및 경로로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-496">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="c8559-497">미들웨어에서 상태 코드를 *301 - 영구적으로 이동됨* 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-497">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="c8559-498">추가 리디렉션 규칙을 요구하지 않고 보안 엔드포인트로 리디렉션하는 경우 HTTPS 리디렉션 미들웨어를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-498">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="c8559-499">자세한 내용은 [HTTPS 적용](xref:security/enforcing-ssl#require-https) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c8559-499">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="c8559-500">예제 응용 프로그램을 통해서 `AddRedirectToHttps` 또는 `AddRedirectToHttpsPermanent`의 사용 방법을 확인해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-500">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="c8559-501">먼저 `RewriteOptions`에 이 확장 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-501">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="c8559-502">모든 URL에서 앱에 대한 안전하지 않은 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-502">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="c8559-503">자체 서명된 인증서를 신뢰할 수 없다는 브라우저 보안 경고는 무시하면 됩니다. 또는 인증서를 신뢰할 예외를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-503">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="c8559-504">`AddRedirectToHttps(301, 5001)`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c8559-504">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="c8559-506">`AddRedirectToHttpsPermanent`에 대한 원본 요청: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="c8559-506">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="c8559-508">URL 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-508">URL rewrite</span></span>

<span data-ttu-id="c8559-509">URL을 재작성하는 규칙을 만들려면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-509">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="c8559-510">첫 번째 매개 변수에는 들어오는 URL의 경로에서 일치하는 정규식이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-510">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="c8559-511">두 번째 매개 변수는 대체 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-511">The second parameter is the replacement string.</span></span> <span data-ttu-id="c8559-512">세 번째 매개 변수, `skipRemainingRules: {true|false}`는 현재 규칙이 적용되는 경우에 추가 재작성 규칙을 건너뛸 것인지 여부를 미들웨어에 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-512">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="c8559-513">원래 요청: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="c8559-513">Original Request: `/rewrite-rule/1234/5678`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="c8559-515">식의 시작 부분에 있는 캐럿(`^`)은 URL 경로의 시작 부분에서 일치가 시작된다는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-515">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="c8559-516">`redirect-rule/(.*)` 리디렉션 규칙이 있는 이전 예제에는 정규식의 시작 부분에 캐럿(`^`)이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-516">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="c8559-517">따라서 일치하는 모든 문자가 경로의 `redirect-rule/` 앞에 나올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-517">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="c8559-518">경로</span><span class="sxs-lookup"><span data-stu-id="c8559-518">Path</span></span>                               | <span data-ttu-id="c8559-519">일치</span><span class="sxs-lookup"><span data-stu-id="c8559-519">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="c8559-520">예</span><span class="sxs-lookup"><span data-stu-id="c8559-520">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="c8559-521">예</span><span class="sxs-lookup"><span data-stu-id="c8559-521">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="c8559-522">예</span><span class="sxs-lookup"><span data-stu-id="c8559-522">Yes</span></span>   |

<span data-ttu-id="c8559-523">반면 `^rewrite-rule/(\d+)/(\d+)` 재작성 규칙의 경우에는 오로지 `rewrite-rule/`로 시작하는 경로만 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-523">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="c8559-524">다음 표에는 일치에서의 차이가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-524">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="c8559-525">경로</span><span class="sxs-lookup"><span data-stu-id="c8559-525">Path</span></span>                              | <span data-ttu-id="c8559-526">일치</span><span class="sxs-lookup"><span data-stu-id="c8559-526">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="c8559-527">예</span><span class="sxs-lookup"><span data-stu-id="c8559-527">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="c8559-528">아니요</span><span class="sxs-lookup"><span data-stu-id="c8559-528">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="c8559-529">아니요</span><span class="sxs-lookup"><span data-stu-id="c8559-529">No</span></span>    |

<span data-ttu-id="c8559-530">표현식의 `^rewrite-rule/` 부분 뒤에는 계속해서 두 개의 캡처 그룹, `(\d+)/(\d+)`이 위치해 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-530">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="c8559-531">여기서 `\d`는 *숫자 하나와 일치* 함을 뜻합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-531">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="c8559-532">그리고 더하기 기호(`+`)는 *앞의 문자와 한 번 이상 일치* 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-532">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="c8559-533">따라서 URL은 반드시 숫자 뒤에 슬래시와 다른 숫자가 연이어 나타나는 부분을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-533">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="c8559-534">이 캡쳐 그룹들은 `$1` 및 `$2`를 통해서 재작성 URL에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-534">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="c8559-535">재작성 규칙의 대체 문자열은 캡처된 그룹을 쿼리 문자열에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-535">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="c8559-536">즉, 요청 경로 `/rewrite-rule/1234/5678`은 `/rewritten?var1=1234&var2=5678`에서 리소스를 가져오도록 재작성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-536">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="c8559-537">원본 요청에 쿼리 문자열이 있으면 URL을 다시 작성할 때 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-537">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="c8559-538">리소스를 가져오기 위해 서버를 왕복하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-538">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="c8559-539">리소스가 있으면 이를 가져와서 *200 - 정상* 상태 코드와 함께 클라이언트에 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-539">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="c8559-540">클라이언트는 리디렉션 되지 않으므로 브라우저 주소 표시줄의 URL은 변경되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-540">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="c8559-541">클라이언트는 서버에서 URL 재작성 작업이 발생했음을 검색할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-541">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="c8559-542">일치 규칙은 컴퓨팅 측면에서 비용이 많이 들고 앱의 응답 속도가 증가되므로 가능한 경우 `skipRemainingRules: true`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-542">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="c8559-543">최대한 빠른 응용 프로그램 응답을 위해서는:</span><span class="sxs-lookup"><span data-stu-id="c8559-543">For the fastest app response:</span></span>
>
> * <span data-ttu-id="c8559-544">재작성 규칙을 가장 자주 일치하는 규칙에서 가장 드물게 일치하는 규칙으로의 순서로 정렬합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-544">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="c8559-545">일치가 발생하고 추가적인 규칙 처리가 필요하지 않다면 나머지 규칙의 처리를 생략합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-545">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="c8559-546">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="c8559-546">Apache mod_rewrite</span></span>

<span data-ttu-id="c8559-547"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>를 사용하면 Apache mod_rewrite 규칙을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-547">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="c8559-548">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-548">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c8559-549">mod_rewrite 규칙에 대한 보다 자세한 내용과 예제는 [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-549">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="c8559-550"><xref:System.IO.StreamReader>는 *ApacheModRewrite.txt* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-550">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="c8559-551">예제 응용 프로그램은 `/apache-mod-rules-redirect/(.\*)`에서 `/redirected?id=$1`로 요청을 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-551">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="c8559-552">응답 상태 코드는 *302 - 있음* 입니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-552">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="c8559-553">원본 요청: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="c8559-553">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="c8559-555">미들웨어는 다음과 같은 Apache mod_rewrite 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="c8559-555">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="c8559-556">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-556">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="c8559-557">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c8559-557">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c8559-558">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c8559-558">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c8559-559">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c8559-559">HTTP_COOKIE</span></span>
* <span data-ttu-id="c8559-560">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="c8559-560">HTTP_FORWARDED</span></span>
* <span data-ttu-id="c8559-561">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c8559-561">HTTP_HOST</span></span>
* <span data-ttu-id="c8559-562">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c8559-562">HTTP_REFERER</span></span>
* <span data-ttu-id="c8559-563">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c8559-563">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c8559-564">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c8559-564">HTTPS</span></span>
* <span data-ttu-id="c8559-565">IPV6</span><span class="sxs-lookup"><span data-stu-id="c8559-565">IPV6</span></span>
* <span data-ttu-id="c8559-566">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c8559-566">QUERY_STRING</span></span>
* <span data-ttu-id="c8559-567">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-567">REMOTE_ADDR</span></span>
* <span data-ttu-id="c8559-568">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c8559-568">REMOTE_PORT</span></span>
* <span data-ttu-id="c8559-569">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c8559-569">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c8559-570">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="c8559-570">REQUEST_METHOD</span></span>
* <span data-ttu-id="c8559-571">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="c8559-571">REQUEST_SCHEME</span></span>
* <span data-ttu-id="c8559-572">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c8559-572">REQUEST_URI</span></span>
* <span data-ttu-id="c8559-573">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c8559-573">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="c8559-574">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-574">SERVER_ADDR</span></span>
* <span data-ttu-id="c8559-575">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="c8559-575">SERVER_PORT</span></span>
* <span data-ttu-id="c8559-576">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="c8559-576">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="c8559-577">TIME</span><span class="sxs-lookup"><span data-stu-id="c8559-577">TIME</span></span>
* <span data-ttu-id="c8559-578">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="c8559-578">TIME_DAY</span></span>
* <span data-ttu-id="c8559-579">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="c8559-579">TIME_HOUR</span></span>
* <span data-ttu-id="c8559-580">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="c8559-580">TIME_MIN</span></span>
* <span data-ttu-id="c8559-581">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="c8559-581">TIME_MON</span></span>
* <span data-ttu-id="c8559-582">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="c8559-582">TIME_SEC</span></span>
* <span data-ttu-id="c8559-583">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="c8559-583">TIME_WDAY</span></span>
* <span data-ttu-id="c8559-584">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="c8559-584">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="c8559-585">IIS URL 재작성 모듈 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-585">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="c8559-586">IIS URL 재작성 모듈에 적용되는 것과 동일한 규칙 세트를 사용하려면 <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-586">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="c8559-587">규칙 파일이 응용 프로그램과 함께 배포되고 있는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-587">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="c8559-588">Windows Server IIS에서 실행하는 경우 미들웨어에서 앱의 *web.config* 파일을 사용하도록 지시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-588">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="c8559-589">IIS를 사용하는 경우 IIS 재작성 모듈과 충돌하지 않도록 이러한 규칙을 앱의 *web.config* 파일 외부에 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-589">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="c8559-590">IIS URL 재작성 모듈 규칙에 대한 보다 자세한 내용 및 예제는 [URL 재작성 모듈 2.0 사용](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)과 [URL 재작성 모듈 구성 참조](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-590">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="c8559-591"><xref:System.IO.StreamReader>는 *IISUrlRewrite.xml* 규칙 파일에서 규칙을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-591">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="c8559-592">예제 응용 프로그램은 `/iis-rules-rewrite/(.*)`에서 `/rewritten?id=$1`로 요청을 재작성합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-592">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="c8559-593">응답이 *200 - 정상* 상태 코드와 함께 클라이언트에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-593">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="c8559-594">원본 요청: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="c8559-594">Original Request: `/iis-rules-rewrite/1234`</span></span>

![요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="c8559-596">원하지 않는 방식으로 응용 프로그램에 영향을 주는 서버 수준 규칙이 구성된 활성 IIS 재작성 모듈이 존재할 경우 응용 프로그램에 대한 IIS 재작성 모듈을 비활성화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-596">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="c8559-597">보다 자세한 내용은 [IIS 모듈 비활성화](xref:host-and-deploy/iis/modules#disabling-iis-modules)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-597">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="c8559-598">지원되지 않는 기능</span><span class="sxs-lookup"><span data-stu-id="c8559-598">Unsupported features</span></span>

<span data-ttu-id="c8559-599">ASP.NET Core 2.x로 출시된 미들웨어는 다음과 같은 IIS URL 재작성 모듈 기능을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-599">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="c8559-600">아웃바운드 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-600">Outbound Rules</span></span>
* <span data-ttu-id="c8559-601">사용자 지정 서버 변수</span><span class="sxs-lookup"><span data-stu-id="c8559-601">Custom Server Variables</span></span>
* <span data-ttu-id="c8559-602">와일드카드</span><span class="sxs-lookup"><span data-stu-id="c8559-602">Wildcards</span></span>
* <span data-ttu-id="c8559-603">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="c8559-603">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="c8559-604">지원되는 서버 변수</span><span class="sxs-lookup"><span data-stu-id="c8559-604">Supported server variables</span></span>

<span data-ttu-id="c8559-605">미들웨어는 다음과 같은 IIS URL 재작성 모듈 서버 변수를 지원합니다:</span><span class="sxs-lookup"><span data-stu-id="c8559-605">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="c8559-606">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="c8559-606">CONTENT_LENGTH</span></span>
* <span data-ttu-id="c8559-607">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="c8559-607">CONTENT_TYPE</span></span>
* <span data-ttu-id="c8559-608">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="c8559-608">HTTP_ACCEPT</span></span>
* <span data-ttu-id="c8559-609">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="c8559-609">HTTP_CONNECTION</span></span>
* <span data-ttu-id="c8559-610">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="c8559-610">HTTP_COOKIE</span></span>
* <span data-ttu-id="c8559-611">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="c8559-611">HTTP_HOST</span></span>
* <span data-ttu-id="c8559-612">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="c8559-612">HTTP_REFERER</span></span>
* <span data-ttu-id="c8559-613">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="c8559-613">HTTP_URL</span></span>
* <span data-ttu-id="c8559-614">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="c8559-614">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="c8559-615">HTTPS</span><span class="sxs-lookup"><span data-stu-id="c8559-615">HTTPS</span></span>
* <span data-ttu-id="c8559-616">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-616">LOCAL_ADDR</span></span>
* <span data-ttu-id="c8559-617">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="c8559-617">QUERY_STRING</span></span>
* <span data-ttu-id="c8559-618">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="c8559-618">REMOTE_ADDR</span></span>
* <span data-ttu-id="c8559-619">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="c8559-619">REMOTE_PORT</span></span>
* <span data-ttu-id="c8559-620">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="c8559-620">REQUEST_FILENAME</span></span>
* <span data-ttu-id="c8559-621">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="c8559-621">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="c8559-622"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>를 이용해서 <xref:Microsoft.Extensions.FileProviders.IFileProvider>를 가져올 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-622">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="c8559-623">이 방식이 재작성 규칙 파일의 위치에 대해 더 많은 유연성을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-623">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="c8559-624">재작성 규칙 파일이 서버의 지정한 경로에 배포되는지 확인하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-624">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="c8559-625">메서드 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-625">Method-based rule</span></span>

<span data-ttu-id="c8559-626">메서드를 이용해서 직접 규칙 로직을 구현하고 싶다면 <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-626">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="c8559-627">`Add`는 메서드에서 사용할 <xref:Microsoft.AspNetCore.Http.HttpContext>를 사용할 수 있게 하는 <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-627">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="c8559-628">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*)는 추가 파이프라인 처리가 수행되는 방법을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-628">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="c8559-629">값을 다음 표에 설명된 <xref:Microsoft.AspNetCore.Rewrite.RuleResult> 필드 중 하나로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-629">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="c8559-630">컨텍스트 결과 다시 작성</span><span class="sxs-lookup"><span data-stu-id="c8559-630">Rewrite context result</span></span>               | <span data-ttu-id="c8559-631">작업</span><span class="sxs-lookup"><span data-stu-id="c8559-631">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="c8559-632">`RuleResult.ContinueRules`(기본값)</span><span class="sxs-lookup"><span data-stu-id="c8559-632">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="c8559-633">규칙 적용을 계속합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-633">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="c8559-634">규칙 적용을 중지하고 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-634">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="c8559-635">규칙 적용을 중지하고 컨텍스트를 다음 미들웨어로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-635">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="c8559-636">예제 응용 프로그램은 *.xml* 로 끝나는 경로 요청을 리디렉션하는 메서드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-636">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="c8559-637">`/file.xml`에 대한 요청이 수행되면 해당 요청이 `/xmlfiles/file.xml`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-637">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="c8559-638">상태 코드는 *301 - 영구적으로 이동됨* 으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-638">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="c8559-639">브라우저에서 */xmlfiles/file.xml* 에 대한 새 요청이 수행되면 정적 파일 미들웨어에서 *wwwroot/xmlfiles* 폴더의 파일을 클라이언트에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-639">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="c8559-640">리디렉션의 경우 응답의 상태 코드를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-640">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="c8559-641">그렇지 않으면 *200 - 정상* 상태 코드가 반환되고 클라이언트에서 리디렉션이 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-641">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="c8559-642">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8559-642">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="c8559-643">또한 이 방법은 요청을 다시 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-643">This approach can also rewrite requests.</span></span> <span data-ttu-id="c8559-644">샘플 앱에서는 *wwwroot* 폴더의 *file.txt* 텍스트 파일을 제공할 텍스트 파일 요청의 경로를 다시 작성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-644">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="c8559-645">정적 파일 미들웨어는 업데이트된 요청 경로에 따라 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-645">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="c8559-646">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="c8559-646">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="c8559-647">IRule 기반 규칙</span><span class="sxs-lookup"><span data-stu-id="c8559-647">IRule-based rule</span></span>

<span data-ttu-id="c8559-648"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>를 사용하여 <xref:Microsoft.AspNetCore.Rewrite.IRule> 인터페이스를 구현하는 클래스에서 규칙 논리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-648">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="c8559-649">`IRule`은 메서드 기반 규칙 방식을 사용하는 것보다 더 높은 유연성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-649">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="c8559-650">구현 클래스에는 <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> 메서드에 대한 매개 변수를 전달할 수 있는 생성자가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-650">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="c8559-651">예제 응용 프로그램은 `extension` 및 `newPath` 매개 변수 값들이 다양한 조건을 만족하는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-651">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="c8559-652">`extension`매개 변수는 값을 포함하고 있어야 하고, 그 값은 *.png*, *.jpg*, 또는 *.gif* 중 하나이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-652">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="c8559-653">만약 `newPath`가 유효하지 않으면 <xref:System.ArgumentException>이 던져집니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-653">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="c8559-654">*image.png* 에 대한 요청이 수행되면 해당 요청이 `/png-images/image.png`으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-654">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="c8559-655">*image.jpg* 에 대한 요청이 수행되면 해당 요청이 `/jpg-images/image.jpg`로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-655">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="c8559-656">상태 코드는 *301 - 영구적으로 이동됨* 으로 설정되고, `context.Result`는 규칙 처리를 중지하고 응답을 보내도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c8559-656">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="c8559-657">원본 요청: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="c8559-657">Original Request: `/image.png`</span></span>

![image.png에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="c8559-659">원본 요청: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="c8559-659">Original Request: `/image.jpg`</span></span>

![image.jpg에 대한 요청 및 응답을 추적하는 개발자 도구가 있는 브라우저 창](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="c8559-661">정규식 예제</span><span class="sxs-lookup"><span data-stu-id="c8559-661">Regex examples</span></span>

| <span data-ttu-id="c8559-662">Goal</span><span class="sxs-lookup"><span data-stu-id="c8559-662">Goal</span></span> | <span data-ttu-id="c8559-663">정규식 문자열 및</span><span class="sxs-lookup"><span data-stu-id="c8559-663">Regex String &</span></span><br><span data-ttu-id="c8559-664">일치 예제</span><span class="sxs-lookup"><span data-stu-id="c8559-664">Match Example</span></span> | <span data-ttu-id="c8559-665">대체 문자열 및</span><span class="sxs-lookup"><span data-stu-id="c8559-665">Replacement String &</span></span><br><span data-ttu-id="c8559-666">출력 예제</span><span class="sxs-lookup"><span data-stu-id="c8559-666">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="c8559-667">경로를 쿼리 문자열로 재작성</span><span class="sxs-lookup"><span data-stu-id="c8559-667">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="c8559-668">후행 슬래시 제거</span><span class="sxs-lookup"><span data-stu-id="c8559-668">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="c8559-669">후행 슬래시 적용</span><span class="sxs-lookup"><span data-stu-id="c8559-669">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="c8559-670">특정 요청 재작성 방지</span><span class="sxs-lookup"><span data-stu-id="c8559-670">Avoid rewriting specific requests</span></span> | <span data-ttu-id="c8559-671">`^(.*)(?<!\.axd)$` 또는 `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="c8559-671">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="c8559-672">예: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="c8559-672">Yes: `/resource.htm`</span></span><br><span data-ttu-id="c8559-673">아니요: `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="c8559-673">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="c8559-674">URL 세그먼트 재정렬</span><span class="sxs-lookup"><span data-stu-id="c8559-674">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="c8559-675">URL 세그먼트 대체</span><span class="sxs-lookup"><span data-stu-id="c8559-675">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c8559-676">추가 자료</span><span class="sxs-lookup"><span data-stu-id="c8559-676">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="c8559-677">.NET에서의 정규식</span><span class="sxs-lookup"><span data-stu-id="c8559-677">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="c8559-678">정규식 언어 - 빠른 참조</span><span class="sxs-lookup"><span data-stu-id="c8559-678">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="c8559-679">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="c8559-679">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="c8559-680">Url 재작성 모듈 2.0 사용(IIS용)</span><span class="sxs-lookup"><span data-stu-id="c8559-680">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="c8559-681">URL 재작성 모듈 구성 참조</span><span class="sxs-lookup"><span data-stu-id="c8559-681">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="c8559-682">IIS URL 재작성 모듈 포럼</span><span class="sxs-lookup"><span data-stu-id="c8559-682">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="c8559-683">간단한 URL 구조 유지</span><span class="sxs-lookup"><span data-stu-id="c8559-683">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="c8559-684">10가지 URL 재작성 팁과 요령</span><span class="sxs-lookup"><span data-stu-id="c8559-684">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="c8559-685">슬래시 여부</span><span class="sxs-lookup"><span data-stu-id="c8559-685">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
