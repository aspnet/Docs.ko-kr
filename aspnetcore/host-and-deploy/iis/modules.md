---
title: IIS 모듈 및 ASP.NET Core
author: rick-anderson
description: ASP.NET Core 앱용 활성 및 비활성 IIS 모듈과 IIS 모듈을 관리하는 방법을 살펴봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 47ba04f199f9b77cf6032de9f80f2410f5c69424
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057403"
---
# <a name="iis-modules-with-aspnet-core"></a><span data-ttu-id="1c63a-103">IIS 모듈 및 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c63a-103">IIS modules with ASP.NET Core</span></span>

<span data-ttu-id="1c63a-104">일부 네이티브 IIS 모듈과 모든 IIS 관리 모듈은 ASP.NET Core 앱에 대한 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-104">Some of the native IIS modules and all of the IIS managed modules aren't able to process requests for ASP.NET Core apps.</span></span> <span data-ttu-id="1c63a-105">대부분의 경우 ASP.NET Core는 IIS 네이티브 및 관리 모듈에서 처리되는 시나리오의 대안을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-105">In many cases, ASP.NET Core offers an alternative to the scenarios addressed by IIS native and managed modules.</span></span>

## <a name="native-modules"></a><span data-ttu-id="1c63a-106">네이티브 모듈</span><span class="sxs-lookup"><span data-stu-id="1c63a-106">Native modules</span></span>

<span data-ttu-id="1c63a-107">이 표는 ASP.NET Core 앱 및 ASP.NET Core 모듈에서 작동하는 네이티브 IIS 모듈을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-107">The table indicates native IIS modules that are functional with ASP.NET Core apps and the ASP.NET Core Module.</span></span>

| <span data-ttu-id="1c63a-108">Module</span><span class="sxs-lookup"><span data-stu-id="1c63a-108">Module</span></span> | <span data-ttu-id="1c63a-109">ASP.NET Core 앱 기능</span><span class="sxs-lookup"><span data-stu-id="1c63a-109">Functional with ASP.NET Core apps</span></span> | <span data-ttu-id="1c63a-110">ASP.NET Core 옵션</span><span class="sxs-lookup"><span data-stu-id="1c63a-110">ASP.NET Core Option</span></span> |
| --- | :---: | --- |
| <span data-ttu-id="1c63a-111">**익명 인증**</span><span class="sxs-lookup"><span data-stu-id="1c63a-111">**Anonymous Authentication**</span></span><br>`AnonymousAuthenticationModule`                                  | <span data-ttu-id="1c63a-112">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-112">Yes</span></span> | |
| <span data-ttu-id="1c63a-113">**기본 인증**</span><span class="sxs-lookup"><span data-stu-id="1c63a-113">**Basic Authentication**</span></span><br>`BasicAuthenticationModule`                                          | <span data-ttu-id="1c63a-114">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-114">Yes</span></span> | |
| <span data-ttu-id="1c63a-115">**클라이언트 인증 매핑 인증**</span><span class="sxs-lookup"><span data-stu-id="1c63a-115">**Client Certification Mapping Authentication**</span></span><br>`CertificateMappingAuthenticationModule`      | <span data-ttu-id="1c63a-116">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-116">Yes</span></span> | |
| <span data-ttu-id="1c63a-117">**CGI**</span><span class="sxs-lookup"><span data-stu-id="1c63a-117">**CGI**</span></span><br>`CgiModule`                                                                           | <span data-ttu-id="1c63a-118">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-118">No</span></span>  | |
| <span data-ttu-id="1c63a-119">**구성 유효성 검사**</span><span class="sxs-lookup"><span data-stu-id="1c63a-119">**Configuration Validation**</span></span><br>`ConfigurationValidationModule`                                  | <span data-ttu-id="1c63a-120">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-120">Yes</span></span> | |
| <span data-ttu-id="1c63a-121">**HTTP 오류**</span><span class="sxs-lookup"><span data-stu-id="1c63a-121">**HTTP Errors**</span></span><br>`CustomErrorModule`                                                           | <span data-ttu-id="1c63a-122">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-122">No</span></span>  | [<span data-ttu-id="1c63a-123">상태 코드 페이지 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-123">Status Code Pages Middleware</span></span>](xref:fundamentals/error-handling#usestatuscodepages) |
| <span data-ttu-id="1c63a-124">**사용자 지정 로깅**</span><span class="sxs-lookup"><span data-stu-id="1c63a-124">**Custom Logging**</span></span><br>`CustomLoggingModule`                                                      | <span data-ttu-id="1c63a-125">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-125">Yes</span></span> | |
| <span data-ttu-id="1c63a-126">**기본 문서**</span><span class="sxs-lookup"><span data-stu-id="1c63a-126">**Default Document**</span></span><br>`DefaultDocumentModule`                                                  | <span data-ttu-id="1c63a-127">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-127">No</span></span>  | [<span data-ttu-id="1c63a-128">기본 파일 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-128">Default Files Middleware</span></span>](xref:fundamentals/static-files#serve-a-default-document) |
| <span data-ttu-id="1c63a-129">**다이제스트 인증**</span><span class="sxs-lookup"><span data-stu-id="1c63a-129">**Digest Authentication**</span></span><br>`DigestAuthenticationModule`                                        | <span data-ttu-id="1c63a-130">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-130">Yes</span></span> | |
| <span data-ttu-id="1c63a-131">**디렉터리 검색**</span><span class="sxs-lookup"><span data-stu-id="1c63a-131">**Directory Browsing**</span></span><br>`DirectoryListingModule`                                               | <span data-ttu-id="1c63a-132">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-132">No</span></span>  | [<span data-ttu-id="1c63a-133">디렉터리 검색 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-133">Directory Browsing Middleware</span></span>](xref:fundamentals/static-files#enable-directory-browsing) |
| <span data-ttu-id="1c63a-134">**동적 압축**</span><span class="sxs-lookup"><span data-stu-id="1c63a-134">**Dynamic Compression**</span></span><br>`DynamicCompressionModule`                                            | <span data-ttu-id="1c63a-135">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-135">Yes</span></span> | [<span data-ttu-id="1c63a-136">응답 압축 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-136">Response Compression Middleware</span></span>](xref:performance/response-compression) |
| <span data-ttu-id="1c63a-137">**실패한 요청 추적**</span><span class="sxs-lookup"><span data-stu-id="1c63a-137">**Failed Requests Tracing**</span></span><br>`FailedRequestsTracingModule`                                     | <span data-ttu-id="1c63a-138">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-138">Yes</span></span> | [<span data-ttu-id="1c63a-139">ASP.NET Core 로깅</span><span class="sxs-lookup"><span data-stu-id="1c63a-139">ASP.NET Core Logging</span></span>](xref:fundamentals/logging/index#tracesource-provider) |
| <span data-ttu-id="1c63a-140">**파일 캐싱**</span><span class="sxs-lookup"><span data-stu-id="1c63a-140">**File Caching**</span></span><br>`FileCacheModule`                                                            | <span data-ttu-id="1c63a-141">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-141">No</span></span>  | [<span data-ttu-id="1c63a-142">응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-142">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="1c63a-143">**HTTP 캐싱**</span><span class="sxs-lookup"><span data-stu-id="1c63a-143">**HTTP Caching**</span></span><br>`HttpCacheModule`                                                            | <span data-ttu-id="1c63a-144">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-144">No</span></span>  | [<span data-ttu-id="1c63a-145">응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-145">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="1c63a-146">**HTTP 로깅**</span><span class="sxs-lookup"><span data-stu-id="1c63a-146">**HTTP Logging**</span></span><br>`HttpLoggingModule`                                                          | <span data-ttu-id="1c63a-147">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-147">Yes</span></span> | [<span data-ttu-id="1c63a-148">ASP.NET Core 로깅</span><span class="sxs-lookup"><span data-stu-id="1c63a-148">ASP.NET Core Logging</span></span>](xref:fundamentals/logging/index) |
| <span data-ttu-id="1c63a-149">**HTTP 리디렉션**</span><span class="sxs-lookup"><span data-stu-id="1c63a-149">**HTTP Redirection**</span></span><br>`HttpRedirectionModule`                                                  | <span data-ttu-id="1c63a-150">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-150">Yes</span></span> | [<span data-ttu-id="1c63a-151">URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-151">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="1c63a-152">**HTTP 추적**</span><span class="sxs-lookup"><span data-stu-id="1c63a-152">**HTTP Tracing**</span></span><br>`TracingModule`                                                              | <span data-ttu-id="1c63a-153">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-153">Yes</span></span> | |
| <span data-ttu-id="1c63a-154">**IIS 클라이언트 인증서 매핑 인증**</span><span class="sxs-lookup"><span data-stu-id="1c63a-154">**IIS Client Certificate Mapping Authentication**</span></span><br>`IISCertificateMappingAuthenticationModule` | <span data-ttu-id="1c63a-155">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-155">Yes</span></span> | |
| <span data-ttu-id="1c63a-156">**IP 및 도메인 제한**</span><span class="sxs-lookup"><span data-stu-id="1c63a-156">**IP and Domain Restrictions**</span></span><br>`IpRestrictionModule`                                          | <span data-ttu-id="1c63a-157">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-157">Yes</span></span> | |
| <span data-ttu-id="1c63a-158">**ISAPI 필터**</span><span class="sxs-lookup"><span data-stu-id="1c63a-158">**ISAPI Filters**</span></span><br>`IsapiFilterModule`                                                         | <span data-ttu-id="1c63a-159">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-159">Yes</span></span> | [<span data-ttu-id="1c63a-160">미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-160">Middleware</span></span>](xref:fundamentals/middleware/index) |
| <span data-ttu-id="1c63a-161">**ISAPI**</span><span class="sxs-lookup"><span data-stu-id="1c63a-161">**ISAPI**</span></span><br>`IsapiModule`                                                                       | <span data-ttu-id="1c63a-162">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-162">Yes</span></span> | [<span data-ttu-id="1c63a-163">미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-163">Middleware</span></span>](xref:fundamentals/middleware/index) |
| <span data-ttu-id="1c63a-164">**프로토콜 지원**</span><span class="sxs-lookup"><span data-stu-id="1c63a-164">**Protocol Support**</span></span><br>`ProtocolSupportModule`                                                  | <span data-ttu-id="1c63a-165">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-165">Yes</span></span> | |
| <span data-ttu-id="1c63a-166">**요청 필터링**</span><span class="sxs-lookup"><span data-stu-id="1c63a-166">**Request Filtering**</span></span><br>`RequestFilteringModule`                                                | <span data-ttu-id="1c63a-167">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-167">Yes</span></span> | [<span data-ttu-id="1c63a-168">URL 재작성 미들웨어`IRule`</span><span class="sxs-lookup"><span data-stu-id="1c63a-168">URL Rewriting Middleware `IRule`</span></span>](xref:fundamentals/url-rewriting#irule-based-rule) |
| <span data-ttu-id="1c63a-169">**요청 모니터**</span><span class="sxs-lookup"><span data-stu-id="1c63a-169">**Request Monitor**</span></span><br>`RequestMonitorModule`                                                    | <span data-ttu-id="1c63a-170">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-170">Yes</span></span> | |
| <span data-ttu-id="1c63a-171">**URL 재작성** &#8224;</span><span class="sxs-lookup"><span data-stu-id="1c63a-171">**URL Rewriting** &#8224;</span></span><br>`RewriteModule`                                                      | <span data-ttu-id="1c63a-172">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-172">Yes</span></span> | [<span data-ttu-id="1c63a-173">URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-173">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="1c63a-174">**서버 쪽 포함**</span><span class="sxs-lookup"><span data-stu-id="1c63a-174">**Server-Side Includes**</span></span><br>`ServerSideIncludeModule`                                            | <span data-ttu-id="1c63a-175">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-175">No</span></span>  | |
| <span data-ttu-id="1c63a-176">**정적 압축**</span><span class="sxs-lookup"><span data-stu-id="1c63a-176">**Static Compression**</span></span><br>`StaticCompressionModule`                                              | <span data-ttu-id="1c63a-177">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-177">No</span></span>  | [<span data-ttu-id="1c63a-178">응답 압축 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-178">Response Compression Middleware</span></span>](xref:performance/response-compression) |
| <span data-ttu-id="1c63a-179">**정적 콘텐츠**</span><span class="sxs-lookup"><span data-stu-id="1c63a-179">**Static Content**</span></span><br>`StaticFileModule`                                                         | <span data-ttu-id="1c63a-180">아니요</span><span class="sxs-lookup"><span data-stu-id="1c63a-180">No</span></span>  | [<span data-ttu-id="1c63a-181">정적 파일 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-181">Static File Middleware</span></span>](xref:fundamentals/static-files) |
| <span data-ttu-id="1c63a-182">**토큰 캐싱**</span><span class="sxs-lookup"><span data-stu-id="1c63a-182">**Token Caching**</span></span><br>`TokenCacheModule`                                                          | <span data-ttu-id="1c63a-183">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-183">Yes</span></span> | |
| <span data-ttu-id="1c63a-184">**URI 캐싱**</span><span class="sxs-lookup"><span data-stu-id="1c63a-184">**URI Caching**</span></span><br>`UriCacheModule`                                                              | <span data-ttu-id="1c63a-185">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-185">Yes</span></span> | |
| <span data-ttu-id="1c63a-186">**URL 권한 부여**</span><span class="sxs-lookup"><span data-stu-id="1c63a-186">**URL Authorization**</span></span><br>`UrlAuthorizationModule`                                                | <span data-ttu-id="1c63a-187">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-187">Yes</span></span> | [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) |
| <span data-ttu-id="1c63a-188">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="1c63a-188">**Windows Authentication**</span></span><br>`WindowsAuthenticationModule`                                      | <span data-ttu-id="1c63a-189">예</span><span class="sxs-lookup"><span data-stu-id="1c63a-189">Yes</span></span> | |

<span data-ttu-id="1c63a-190">&#8224;URL 재작성 모듈의 `isFile` 및 `isDirectory` 일치 유형이 [디렉터리 구조](xref:host-and-deploy/directory-structure)의 변경으로 인해 ASP.NET Core 앱에서 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-190">&#8224;The URL Rewrite Module's `isFile` and `isDirectory` match types don't work with ASP.NET Core apps due to the changes in [directory structure](xref:host-and-deploy/directory-structure).</span></span>

## <a name="managed-modules"></a><span data-ttu-id="1c63a-191">관리 모듈</span><span class="sxs-lookup"><span data-stu-id="1c63a-191">Managed modules</span></span>

<span data-ttu-id="1c63a-192">앱 풀의 .NET CLR 버전이 **관리 코드 없음** 으로 설정된 경우 관리 모듈은 호스트된 ASP.NET Core 앱에서 작동하지 ‘않습니다’.</span><span class="sxs-lookup"><span data-stu-id="1c63a-192">Managed modules are *not* functional with hosted ASP.NET Core apps when the app pool's .NET CLR version is set to **No Managed Code**.</span></span> <span data-ttu-id="1c63a-193">ASP.NET Core는 여러 경우에 미들웨어 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-193">ASP.NET Core offers middleware alternatives in several cases.</span></span>

| <span data-ttu-id="1c63a-194">Module</span><span class="sxs-lookup"><span data-stu-id="1c63a-194">Module</span></span>                  | <span data-ttu-id="1c63a-195">ASP.NET Core 옵션</span><span class="sxs-lookup"><span data-stu-id="1c63a-195">ASP.NET Core Option</span></span> |
| ----------------------- | ------------------- |
| <span data-ttu-id="1c63a-196">AnonymousIdentification</span><span class="sxs-lookup"><span data-stu-id="1c63a-196">AnonymousIdentification</span></span> | |
| <span data-ttu-id="1c63a-197">DefaultAuthentication</span><span class="sxs-lookup"><span data-stu-id="1c63a-197">DefaultAuthentication</span></span>   | |
| <span data-ttu-id="1c63a-198">FileAuthorization</span><span class="sxs-lookup"><span data-stu-id="1c63a-198">FileAuthorization</span></span>       | |
| <span data-ttu-id="1c63a-199">FormsAuthentication</span><span class="sxs-lookup"><span data-stu-id="1c63a-199">FormsAuthentication</span></span>     | <span data-ttu-id="1c63a-200">[:::no-loc(Cookie)::: 인증 미들웨어](xref:security/authentication/:::no-loc(cookie):::)</span><span class="sxs-lookup"><span data-stu-id="1c63a-200">[:::no-loc(Cookie)::: Authentication Middleware](xref:security/authentication/:::no-loc(cookie):::)</span></span> |
| <span data-ttu-id="1c63a-201">OutputCache</span><span class="sxs-lookup"><span data-stu-id="1c63a-201">OutputCache</span></span>             | [<span data-ttu-id="1c63a-202">응답 캐싱 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-202">Response Caching Middleware</span></span>](xref:performance/caching/middleware) |
| <span data-ttu-id="1c63a-203">프로필</span><span class="sxs-lookup"><span data-stu-id="1c63a-203">Profile</span></span>                 | |
| <span data-ttu-id="1c63a-204">RoleManager</span><span class="sxs-lookup"><span data-stu-id="1c63a-204">RoleManager</span></span>             | |
| <span data-ttu-id="1c63a-205">ScriptModule-4.0</span><span class="sxs-lookup"><span data-stu-id="1c63a-205">ScriptModule-4.0</span></span>        | |
| <span data-ttu-id="1c63a-206">세션</span><span class="sxs-lookup"><span data-stu-id="1c63a-206">Session</span></span>                 | [<span data-ttu-id="1c63a-207">세션 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-207">Session Middleware</span></span>](xref:fundamentals/app-state) |
| <span data-ttu-id="1c63a-208">UrlAuthorization</span><span class="sxs-lookup"><span data-stu-id="1c63a-208">UrlAuthorization</span></span>        | |
| <span data-ttu-id="1c63a-209">UrlMappingsModule</span><span class="sxs-lookup"><span data-stu-id="1c63a-209">UrlMappingsModule</span></span>       | [<span data-ttu-id="1c63a-210">URL 재작성 미들웨어</span><span class="sxs-lookup"><span data-stu-id="1c63a-210">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting) |
| <span data-ttu-id="1c63a-211">UrlRoutingModule-4.0</span><span class="sxs-lookup"><span data-stu-id="1c63a-211">UrlRoutingModule-4.0</span></span>    | [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) |
| <span data-ttu-id="1c63a-212">WindowsAuthentication</span><span class="sxs-lookup"><span data-stu-id="1c63a-212">WindowsAuthentication</span></span>   | |

## <a name="iis-manager-application-changes"></a><span data-ttu-id="1c63a-213">IIS 관리자 애플리케이션 변경 내용</span><span class="sxs-lookup"><span data-stu-id="1c63a-213">IIS Manager application changes</span></span>

<span data-ttu-id="1c63a-214">IIS 관리자를 사용하여 설정을 구성하는 경우 앱의 *web.config* 파일이 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-214">When using IIS Manager to configure settings, the *web.config* file of the app is changed.</span></span> <span data-ttu-id="1c63a-215">앱을 배포하고 *web.config* 를 포함하는 경우 IIS 관리자를 사용하여 변경한 모든 내용을 배포된 *web.config* 파일이 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-215">If deploying an app and including *web.config* , any changes made with IIS Manager are overwritten by the deployed *web.config* file.</span></span> <span data-ttu-id="1c63a-216">서버의 *web.config* 파일을 변경하는 경우 서버의 업데이트된 *web.config* 파일을 로컬 프로젝트에 즉시 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-216">If changes are made to the server's *web.config* file, copy the updated *web.config* file on the server to the local project immediately.</span></span>

## <a name="disabling-iis-modules"></a><span data-ttu-id="1c63a-217">IIS 모듈 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="1c63a-217">Disabling IIS modules</span></span>

<span data-ttu-id="1c63a-218">IIS 모듈이 앱에 대해 사용되지 않아야 하는 서버 수준에서 구성된 경우 앱의 *web.config* 파일에 추가하여 모듈을 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-218">If an IIS module is configured at the server level that must be disabled for an app, an addition to the app's *web.config* file can disable the module.</span></span> <span data-ttu-id="1c63a-219">모듈을 제자리에 두고 구성 설정(사용 가능한 경우)을 사용하여 모듈을 비활성화하거나 앱에서 모듈을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-219">Either leave the module in place and deactivate it using a configuration setting (if available) or remove the module from the app.</span></span>

### <a name="module-deactivation"></a><span data-ttu-id="1c63a-220">모듈 비활성화</span><span class="sxs-lookup"><span data-stu-id="1c63a-220">Module deactivation</span></span>

<span data-ttu-id="1c63a-221">많은 모듈에서는 앱에서 모듈을 제거하지 않고도 사용하지 않도록 설정할 수 있는 구성 설정을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-221">Many modules offer a configuration setting that allows them to be disabled without removing the module from the app.</span></span> <span data-ttu-id="1c63a-222">이는 모듈을 비활성화하는 가장 간단하고 가장 빠른 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-222">This is the simplest and quickest way to deactivate a module.</span></span> <span data-ttu-id="1c63a-223">예를 들어 HTTP 리디렉션 모듈은 *web.config* 의 `<httpRedirect>` 요소로 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-223">For example, the HTTP Redirection Module can be disabled with the `<httpRedirect>` element in *web.config* :</span></span>

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="1c63a-224">구성 설정을 사용하여 모듈을 사용하지 않도록 설정하는 방법에 관한 자세한 내용은 [IIS \<system.webServer>](/iis/configuration/system.webServer/)의 ‘자식 요소’ 섹션에 있는 링크를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1c63a-224">For more information on disabling modules with configuration settings, follow the links in the *Child Elements* section of [IIS \<system.webServer>](/iis/configuration/system.webServer/).</span></span>

### <a name="module-removal"></a><span data-ttu-id="1c63a-225">모듈 제거</span><span class="sxs-lookup"><span data-stu-id="1c63a-225">Module removal</span></span>

<span data-ttu-id="1c63a-226">*web.config* 에서 설정과 함께 모듈을 제거하도록 선택한 경우 먼저 모듈을 잠금 해제하고 *web.config* 의 `<modules>` 섹션을 작금 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-226">If opting to remove a module with a setting in *web.config* , unlock the module and unlock the `<modules>` section of *web.config* first:</span></span>

1. <span data-ttu-id="1c63a-227">서버 수준에서 모듈의 잠금을 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-227">Unlock the module at the server level.</span></span> <span data-ttu-id="1c63a-228">IIS 관리자 **연결** 사이드바에서 IIS 서버를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-228">Select the IIS server in the IIS Manager **Connections** sidebar.</span></span> <span data-ttu-id="1c63a-229">**IIS** 영역에서 **모듈** 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-229">Open the **Modules** in the **IIS** area.</span></span> <span data-ttu-id="1c63a-230">목록에서 모듈을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-230">Select the module in the list.</span></span> <span data-ttu-id="1c63a-231">오른쪽의 **작업** 사이드바에서 **잠금 해제** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-231">In the **Actions** sidebar on the right, select **Unlock**.</span></span> <span data-ttu-id="1c63a-232">모듈에 대한 작업 항목이 **잠금** 으로 나타나면 모듈이 이미 잠금 해제되어 있으며 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-232">If the action entry for the module appears as **Lock** , the module is already unlocked, and no action is required.</span></span> <span data-ttu-id="1c63a-233">나중에 *web.config* 에서 제거하려고 계획한 만큼 모듈을 잠금 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-233">Unlock as many modules as you plan to remove from *web.config* later.</span></span>

2. <span data-ttu-id="1c63a-234">*web.config* 의 `<modules>` 섹션 없이 앱을 배포합니다. IIS 관리자에서 먼저 섹션을 잠금 해제하지 않고 `<modules>` 섹션이 포함된 *web.config* 를 사용하여 앱을 배포하면 Configuration Manager에서 섹션을 잠금 해제하려고 할 때 예외가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-234">Deploy the app without a `<modules>` section in *web.config*. If an app is deployed with a *web.config* containing the `<modules>` section without having unlocked the section first in the IIS Manager, the Configuration Manager throws an exception when attempting to unlock the section.</span></span> <span data-ttu-id="1c63a-235">따라서 `<modules>` 섹션 없이 앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-235">Therefore, deploy the app without a `<modules>` section.</span></span>

3. <span data-ttu-id="1c63a-236">*web.config* 의 `<modules>` 섹션을 잠금 해제합니다. **연결** 사이드바의 **사이트** 에서 웹 사이트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-236">Unlock the `<modules>` section of *web.config*. In the **Connections** sidebar, select the website in **Sites**.</span></span> <span data-ttu-id="1c63a-237">**관리** 영역에서 **구성 편집기** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-237">In the **Management** area, open the **Configuration Editor**.</span></span> <span data-ttu-id="1c63a-238">탐색 컨트롤을 사용하여 `system.webServer/modules` 섹션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-238">Use the navigation controls to select the `system.webServer/modules` section.</span></span> <span data-ttu-id="1c63a-239">오른쪽의 **작업** 사이드바에서 섹션을 **잠금 해제** 하도록 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-239">In the **Actions** sidebar on the right, select to **Unlock** the section.</span></span> <span data-ttu-id="1c63a-240">모듈 섹션에 대한 작업 항목이 **섹션 잠금** 으로 나타나면 모듈 섹션이 이미 잠금 해제되어 있으며 작업이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-240">If the action entry for the module section appears as **Lock Section** , the module section is already unlocked, and no action is required.</span></span>

4. <span data-ttu-id="1c63a-241">`<modules>` 섹션을 `<remove>` 요소가 포함된 앱의 로컬 *web.config* 파일에 추가하여 앱에서 모듈을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-241">Add a `<modules>` section to the app's local *web.config* file with a `<remove>` element to remove the module from the app.</span></span> <span data-ttu-id="1c63a-242">여러 `<remove>` 요소를 추가하여 여러 모듈을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-242">Add multiple `<remove>` elements to remove multiple modules.</span></span> <span data-ttu-id="1c63a-243">서버에서 *web.config* 가 변경되면 로컬에서 프로젝트의 *web.config* 파일에 동일한 변경 내용이 즉시 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-243">If *web.config* changes are made on the server, immediately make the same changes to the project's *web.config* file locally.</span></span> <span data-ttu-id="1c63a-244">이 방법을 사용하여 모듈을 제거해도 서버의 다른 앱과 함께 모듈을 사용하는 데 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-244">Removing a module using this approach doesn't affect the use of the module with other apps on the server.</span></span>

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

<span data-ttu-id="1c63a-245">*web.config* 를 사용하여 IIS Express용 모듈을 추가 또는 제거하려면 *applicationHost.config* 를 수정하여 `<modules>` 섹션을 잠금 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-245">In order to add or remove modules for IIS Express using *web.config* , modify *applicationHost.config* to unlock the `<modules>` section:</span></span>

1. <span data-ttu-id="1c63a-246">*{APPLICATION ROOT}\\.vs\config\applicationhost.config* 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-246">Open *{APPLICATION ROOT}\\.vs\config\applicationhost.config*.</span></span>

1. <span data-ttu-id="1c63a-247">IIS 모듈에 대한 `<section>` 요소를 찾고 `overrideModeDefault`를 `Deny`에서 `Allow`로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-247">Locate the `<section>` element for IIS modules and change `overrideModeDefault` from `Deny` to `Allow`:</span></span>

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. <span data-ttu-id="1c63a-248">`<location path="" overrideMode="Allow"><system.webServer><modules>` 섹션을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-248">Locate the `<location path="" overrideMode="Allow"><system.webServer><modules>` section.</span></span> <span data-ttu-id="1c63a-249">제거하려는 모듈의 경우 `lockItem`을 `true`에서 `false`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-249">For any modules that you wish to remove, set `lockItem` from `true` to `false`.</span></span> <span data-ttu-id="1c63a-250">다음 예제에서는 CGI 모듈의 잠금이 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-250">In the following example, the CGI Module is unlocked:</span></span>

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. <span data-ttu-id="1c63a-251">`<modules>` 섹션과 개별 모듈의 잠금이 해제된 후에는 IIS Express에서 앱을 실행하기 위한 앱의 *web.config* 파일을 사용하여 IIS 모듈을 추가하거나 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-251">After the `<modules>` section and individual modules are unlocked, you're free to add or remove IIS modules using the app's *web.config* file for running the app on IIS Express.</span></span>

<span data-ttu-id="1c63a-252">IIS 모듈을 *Appcmd.exe* 를 사용하여 제거할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-252">An IIS module can also be removed with *Appcmd.exe*.</span></span> <span data-ttu-id="1c63a-253">다음 명령에 `MODULE_NAME` 및 `APPLICATION_NAME`을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-253">Provide the `MODULE_NAME` and `APPLICATION_NAME` in the command:</span></span>

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

<span data-ttu-id="1c63a-254">예를 들어 기본 웹 사이트에서 `DynamicCompressionModule`을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-254">For example, remove the `DynamicCompressionModule` from the Default Web Site:</span></span>

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a><span data-ttu-id="1c63a-255">최소 모듈 구성</span><span class="sxs-lookup"><span data-stu-id="1c63a-255">Minimum module configuration</span></span>

<span data-ttu-id="1c63a-256">ASP.NET Core 앱을 실행하는 데 필요한 유일한 모듈은 익명 인증 모듈 및 ASP.NET Core 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-256">The only modules required to run an ASP.NET Core app are the Anonymous Authentication Module and the ASP.NET Core Module.</span></span>

<span data-ttu-id="1c63a-257">URI 캐싱 모듈(`UriCacheModule`)을 통해 IIS가 URL 수준에서 웹 사이트 구성을 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-257">The URI Caching Module (`UriCacheModule`) allows IIS to cache website configuration at the URL level.</span></span> <span data-ttu-id="1c63a-258">이 모듈이 없으면 동일한 URL이 반복적으로 요청되더라도 IIS는 요청될 때마다 구성을 읽고 구문 분석해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-258">Without this module, IIS must read and parse configuration on every request, even when the same URL is repeatedly requested.</span></span> <span data-ttu-id="1c63a-259">요청될 때마다 구성을 구문 분석하면 성능이 크게 저하됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-259">Parsing the configuration every request results in a significant performance penalty.</span></span> <span data-ttu-id="1c63a-260">‘URI 캐싱 모듈은 호스트된 ASP.NET Core 앱을 실행하는 데 꼭 필요하지는 않지만, 모든 ASP.NET Core 배포에 대해 URI 캐싱 모듈을 사용하도록 설정하는 것이 좋습니다.’</span><span class="sxs-lookup"><span data-stu-id="1c63a-260">*Although the URI Caching Module isn't strictly required for a hosted ASP.NET Core app to run, we recommend that the URI Caching Module be enabled for all ASP.NET Core deployments.*</span></span>

<span data-ttu-id="1c63a-261">HTTP 캐싱 모듈(`HttpCacheModule`)은 IIS 출력 캐시 및 HTTP.sys 캐시에 있는 항목을 캐시하기 위한 논리를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-261">The HTTP Caching Module (`HttpCacheModule`) implements the IIS output cache and also the logic for caching items in the HTTP.sys cache.</span></span> <span data-ttu-id="1c63a-262">이 모듈이 없으면 콘텐츠가 커널 모드에서 더 이상 캐시되지 않으며 캐시 프로필이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-262">Without this module, content is no longer cached in kernel mode, and cache profiles are ignored.</span></span> <span data-ttu-id="1c63a-263">일반적으로 HTTP 캐싱 모듈을 제거하면 성능 및 리소스 사용에 부정적인 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="1c63a-263">Removing the HTTP Caching Module usually has adverse effects on performance and resource usage.</span></span> <span data-ttu-id="1c63a-264">‘HTTP 캐싱 모듈은 호스트된 ASP.NET Core 앱을 실행하는 데 꼭 필요하지는 않지만, 모든 ASP.NET Core 배포에 대해 HTTP 캐싱 모듈을 사용하도록 설정하는 것이 좋습니다.’</span><span class="sxs-lookup"><span data-stu-id="1c63a-264">*Although the HTTP Caching Module isn't strictly required for a hosted ASP.NET Core app to run, we recommend that the HTTP Caching Module be enabled for all ASP.NET Core deployments.*</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1c63a-265">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1c63a-265">Additional resources</span></span>

* [<span data-ttu-id="1c63a-266">IIS 아키텍처 소개: IIS의 모듈</span><span class="sxs-lookup"><span data-stu-id="1c63a-266">Introduction to IIS Architectures: Modules in IIS</span></span>](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [<span data-ttu-id="1c63a-267">IIS 모듈 개요</span><span class="sxs-lookup"><span data-stu-id="1c63a-267">IIS Modules Overview</span></span>](/iis/get-started/introduction-to-iis/iis-modules-overview)
* <span data-ttu-id="1c63a-268">[IIS 7.0 역할 및 모듈 사용자 지정](/previous-versions/tn-archive/cc627313(v=technet.10))</span><span class="sxs-lookup"><span data-stu-id="1c63a-268">[Customizing IIS 7.0 Roles and Modules](/previous-versions/tn-archive/cc627313(v=technet.10))</span></span>
* [<span data-ttu-id="1c63a-269">IIS \<system.webServer></span><span class="sxs-lookup"><span data-stu-id="1c63a-269">IIS \<system.webServer></span></span>](/iis/configuration/system.webServer/)