---
title: ASP.NET Core 2.0의 새로운 기능
author: rick-anderson
description: ASP.NET Core 2.0의 새로운 기능에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.0
ms.openlocfilehash: b7515bcd8b15199770a4245469d00d10da5566f8
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605689"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="8c301-103">ASP.NET Core 2.0의 새로운 기능</span><span class="sxs-lookup"><span data-stu-id="8c301-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="8c301-104">이 문서에서는 ASP.NET Core 2.0의 가장 큰 변경 내용을 중점적으로 설명하고 관련 문서의 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="8c301-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8c301-105">Razor Pages</span></span>

<span data-ttu-id="8c301-106">Razor Pages는 더 쉽고 더 생산적으로 코딩 페이지에 초점을 맞춘 시나리오를 만드는 ASP.NET Core MVC의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="8c301-107">자세한 내용은 소개 및 자습서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="8c301-108">Razor Pages 소개</span><span class="sxs-lookup"><span data-stu-id="8c301-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="8c301-109">Razor Pages 시작</span><span class="sxs-lookup"><span data-stu-id="8c301-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="8c301-110">ASP.NET Core 메타패키지</span><span class="sxs-lookup"><span data-stu-id="8c301-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="8c301-111">새로운 ASP.NET Core 메타패키지에는 ASP.NET Core 및 Entity Framework Core 팀에서 만들고 지원하는 모든 패키지와 함께 해당하는 내부 및 타사 종속성이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="8c301-112">더 이상 패키지별로 개별 ASP.NET Core 기능을 선택할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="8c301-113">모든 기능은 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 패키지에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="8c301-114">기본 템플릿은 이 패키지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-114">The default templates use this package.</span></span>

<span data-ttu-id="8c301-115">자세한 내용은 [ASP.NET Core 2.0에 대한 Microsoft.AspNetCore.All 메타패키지](xref:fundamentals/metapackage)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="8c301-116">런타임 저장소</span><span class="sxs-lookup"><span data-stu-id="8c301-116">Runtime Store</span></span>

<span data-ttu-id="8c301-117">`Microsoft.AspNetCore.All` 메타패키지를 사용하는 애플리케이션은 새로운 .NET Core 런타임 저장소를 자동으로 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="8c301-118">저장소에는 ASP.NET Core 2.0 애플리케이션을 실행하는 데 필요한 모든 런타임 자산이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="8c301-119">`Microsoft.AspNetCore.All` 메타패키지를 사용할 경우 참조되는 ASP.NET Core NuGet 패키지의 자산은 대상 시스템에 있기 때문에 애플리케이션과 함께 배포되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="8c301-120">또한 애플리케이션 시작 시간을 개선하기 위해 런타임 저장소의 자산은 미리 컴파일됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="8c301-121">자세한 내용은 [런타임 저장소](/dotnet/core/deploying/runtime-store)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="8c301-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="8c301-122">.NET Standard 2.0</span></span>

<span data-ttu-id="8c301-123">ASP.NET Core 2.0 패키지는 .NET Standard 2.0을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="8c301-124">패키지는 다른 .NET Standard 2.0 라이브러리에서 참조될 수 있고 .NET Core 2.0, .NET Framework 4.6.1 등 .NET의 .NET Standard 2.0 규격 구현에서 실행될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="8c301-125">`Microsoft.AspNetCore.All` 메타패키지는 .NET Core 2.0 런타임 저장소와 함께 사용되므로 .NET Core 2.0만 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="8c301-126">구성 업데이트</span><span class="sxs-lookup"><span data-stu-id="8c301-126">Configuration update</span></span>

<span data-ttu-id="8c301-127">`IConfiguration` 인스턴스는 기본적으로 ASP.NET Core 2.0의 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="8c301-128">서비스 컨테이너의 `IConfiguration`을 사용하면 애플리케이션이 컨테이너에서 구성 값을 더 쉽게 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="8c301-129">계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387)(GitHub 문제)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="8c301-130">로깅 업데이트</span><span class="sxs-lookup"><span data-stu-id="8c301-130">Logging update</span></span>

<span data-ttu-id="8c301-131">ASP.NET Core 2.0에서 로깅은 기본적으로 DI(종속성 주입) 시스템에 통합되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="8c301-132">*Startup.cs* 파일 대신에 *Program.cs* 파일에서 공급자를 추가하고 필터링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="8c301-133">기본 `ILoggerFactory`는 전체 공급자 필터링 및 특정 공급자 필터링에 둘 다 하나의 유연한 방법을 사용하는 방식으로 필터링을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="8c301-134">자세한 내용은 [로깅 소개](xref:fundamentals/logging/index)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="8c301-135">인증 업데이트</span><span class="sxs-lookup"><span data-stu-id="8c301-135">Authentication update</span></span>

<span data-ttu-id="8c301-136">새 인증 모델을 사용하면 DI를 사용하는 애플리케이션에 대한 인증을 더 쉽게 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="8c301-137">새 템플릿을 사용하여 [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)를 통해 웹앱과 웹 API에 대한 인증을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="8c301-138">계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054)(GitHub 문제)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="8c301-139">Identity 업데이트</span><span class="sxs-lookup"><span data-stu-id="8c301-139">Identity update</span></span>

<span data-ttu-id="8c301-140">ASP.NET Core 2.0에서는 Identity를 사용하여 보안 Web API를 더 쉽게 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="8c301-141">[MSAL(Microsoft Authentication Library)](https://www.nuget.org/packages/Microsoft.Identity.Client)을 사용하여 Web API에 액세스하기 위해 액세스 토큰을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="8c301-142">2\.0의 인증 변경에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="8c301-143">ASP.NET Core의 계정 확인 및 암호 복구</span><span class="sxs-lookup"><span data-stu-id="8c301-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="8c301-144">ASP.NET Core에서 인증자 앱에 QR 코드 생성 사용</span><span class="sxs-lookup"><span data-stu-id="8c301-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="8c301-145">ASP.NET Core 2.0으로 인증 및 Identity 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8c301-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="8c301-146">SPA 템플릿</span><span class="sxs-lookup"><span data-stu-id="8c301-146">SPA templates</span></span>

<span data-ttu-id="8c301-147">Angular, Aurelia, Knockout.js, React.js 및 React.js에 대한 SPA(단일 페이지 애플리케이션) 프로젝트 템플릿을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="8c301-148">Angular 템플릿은 Angular 4로 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="8c301-149">Angular 및 React 템플릿은 기본적으로 제공됩니다. 다른 템플릿을 가져오는 방법에 대한 자세한 내용은 [새 SPA 프로젝트 만들기](xref:client-side/spa-services#create-a-new-project)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="8c301-150">ASP.NET Core에서 SPA를 빌드하는 방법에 대한 정보는 <xref:client-side/spa-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="8c301-151">Kestrel 기능 향상</span><span class="sxs-lookup"><span data-stu-id="8c301-151">Kestrel improvements</span></span>

<span data-ttu-id="8c301-152">Kestrel 웹 서버에는 인터넷 연결 서버로서 더 적합하도록 도와주는 새로운 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="8c301-153">다양한 서버 제약 조건 구성 옵션이 `KestrelServerOptions` 클래스의 새 `Limits` 속성에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="8c301-154">다음에 대한 제한을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-154">Add limits for the following:</span></span>

* <span data-ttu-id="8c301-155">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="8c301-155">Maximum client connections</span></span>
* <span data-ttu-id="8c301-156">최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="8c301-156">Maximum request body size</span></span>
* <span data-ttu-id="8c301-157">최소 요청 본문 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="8c301-157">Minimum request body data rate</span></span>

<span data-ttu-id="8c301-158">자세한 내용은 [ASP.NET Core의 Kestrel 웹 서버 구현](xref:fundamentals/servers/kestrel)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="8c301-159">WebListener 이름이 HTTP.sys로 바뀜</span><span class="sxs-lookup"><span data-stu-id="8c301-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="8c301-160">패키지 `Microsoft.AspNetCore.Server.WebListener` 및 `Microsoft.Net.Http.Server`가 새 패키지 `Microsoft.AspNetCore.Server.HttpSys`로 병합되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="8c301-161">네임스페이스가 일치하도록 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="8c301-162">자세한 내용은 [ASP.NET Core의 HTTP.sys 웹 서버 구현](xref:fundamentals/servers/httpsys)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="8c301-163">향상된 HTTP 헤더 지원</span><span class="sxs-lookup"><span data-stu-id="8c301-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="8c301-164">MVC를 사용하여 `FileStreamResult` 또는 `FileContentResult`를 전송할 경우 이제 전송하는 콘텐츠에서 `ETag` 또는 `LastModified` 날짜를 설정하는 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="8c301-165">다음과 비슷한 코드를 사용하여 반환된 콘텐츠에서 이러한 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="8c301-166">방문자에게 반환된 파일이 `ETag` 및 `LastModified` 값에 해당하는 HTTP 헤더를 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="8c301-167">애플리케이션 방문자가 범위 요청 헤더가 포함된 콘텐츠를 요청하면 ASP.NET Core는 요청을 인식하고 헤더를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="8c301-168">요청된 콘텐츠가 부분적으로 전달될 수 있으면 ASP.NET Core는 적절히 건너뛰고 요청된 바이트 집합만 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="8c301-169">이 기능에 맞게 조정하거나 이 기능을 처리하기 위해 메서드에 특수 처리기를 작성할 필요가 없습니다. 기능이 자동으로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="8c301-170">호스팅 시작 및 Application Insights</span><span class="sxs-lookup"><span data-stu-id="8c301-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="8c301-171">이제 호스팅 환경에서는 애플리케이션이 명시적으로 종속성을 사용하거나 메서드를 호출할 필요 없이 추가 패키지 종속성을 삽입하고 애플리케이션 시작 중에 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="8c301-172">이 기능을 사용하면 애플리케이션이 미리 인식할 필요 없이 특정 환경이 해당 환경에 고유한 기능을 “강화”할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="8c301-173">ASP.NET Core 2.0에서 이 기능은 Visual Studio에서 디버그할 경우 및 Azure App Services에서 실행될 경우(옵트인(opt in) 후) 자동으로 Application Insights 진단을 사용하도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="8c301-174">따라서 프로젝트 템플릿은 더 이상 기본적으로 Application Insights 패키지와 코드를 추가하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="8c301-175">계획된 문서의 상태에 대한 자세한 내용은 [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389)(GitHub 문제)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="8c301-176">위조 방지 토큰 자동 사용</span><span class="sxs-lookup"><span data-stu-id="8c301-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="8c301-177">ASP.NET Core는 언제나 기본적으로 콘텐츠를 HTML 인코딩하는 기능을 제공했지만, 새 버전에서는 XSRF(교차 사이트 요청 위조) 공격을 방지할 수 있도록 추가 조치를 취합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="8c301-178">이제 ASP.NET Core는 기본적으로 위조 방지 토큰을 내보내고 추가 구성없이 폼 POST 작업 및 페이지에서 토큰의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="8c301-179">자세한 내용은 <xref:security/anti-request-forgery>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-179">For more information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="8c301-180">자동으로 미리 컴파일</span><span class="sxs-lookup"><span data-stu-id="8c301-180">Automatic precompilation</span></span>

<span data-ttu-id="8c301-181">Razor 뷰 미리 컴파일이 기본적으로 게시 중에 사용하도록 설정되므로 게시 출력 크기와 애플리케이션 시작 시간이 감소합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="8c301-182">자세한 내용은 [ASP.NET Core에서 Razor 뷰 컴파일 및 미리 컴파일](xref:mvc/views/view-compilation)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="8c301-183">C# 7.1의 Razor 지원</span><span class="sxs-lookup"><span data-stu-id="8c301-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="8c301-184">Razor 뷰 엔진이 새 Roslyn 컴파일러를 사용하도록 업데이트되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="8c301-185">여기에는 기본 식, 유추된 튜플 이름 및 제네릭 패턴 일치 같은 C# 7.1 기능에 대한 지원이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="8c301-186">프로젝트에서 C# 7.1을 사용하려면 프로젝트 파일에 다음 속성을 추가하고 나서 솔루션을 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="8c301-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="8c301-187">C# 7.1 기능 상태에 대한 자세한 내용은 [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md)(Roslyn GitHub 리포지토리)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="8c301-188">2\.0에 대한 기타 문서 업데이트</span><span class="sxs-lookup"><span data-stu-id="8c301-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="8c301-189">ASP.NET Core 앱 배포용 Visual Studio 게시 프로필</span><span class="sxs-lookup"><span data-stu-id="8c301-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="8c301-190">키 관리</span><span class="sxs-lookup"><span data-stu-id="8c301-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="8c301-191">Facebook 인증 구성</span><span class="sxs-lookup"><span data-stu-id="8c301-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="8c301-192">Twitter 인증 구성</span><span class="sxs-lookup"><span data-stu-id="8c301-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="8c301-193">Google 인증 구성</span><span class="sxs-lookup"><span data-stu-id="8c301-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="8c301-194">Microsoft 계정 인증 구성</span><span class="sxs-lookup"><span data-stu-id="8c301-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="8c301-195">마이그레이션 지침</span><span class="sxs-lookup"><span data-stu-id="8c301-195">Migration guidance</span></span>

<span data-ttu-id="8c301-196">ASP.NET Core 1.x 애플리케이션을 ASP.NET Core 2.0으로 마이그레이션하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="8c301-197">ASP.NET Core 1.x에서 ASP.NET Core 2.0으로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8c301-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="8c301-198">ASP.NET Core 2.0으로 인증 및 Identity 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="8c301-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="8c301-199">추가 정보</span><span class="sxs-lookup"><span data-stu-id="8c301-199">Additional Information</span></span>

<span data-ttu-id="8c301-200">전체 변경 내용 목록을 보려면 [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)(ASP.NET Core 2.0 릴리스 정보)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="8c301-201">ASP.NET Core 개발팀의 진행 상황 및 계획과 연결하려면 [ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)을 시청하세요.</span><span class="sxs-lookup"><span data-stu-id="8c301-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
