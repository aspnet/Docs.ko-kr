---
title: ASP.NET Core MVC에 대한 호환성 버전
author: rick-anderson
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
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
uid: mvc/compatibility-version
ms.openlocfilehash: 9123bd70dfee1578912f682faf0520735fd55776
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051293"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="d73fe-103">ASP.NET Core MVC에 대한 호환성 버전</span><span class="sxs-lookup"><span data-stu-id="d73fe-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="d73fe-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d73fe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d73fe-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드는 ASP.NET Core 3.0 앱에 대해 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="d73fe-106">즉, <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion>의 아무 값으로나 `SetCompatibilityVersion`을 호출해도 해당 애플리케이션에는 영향이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="d73fe-107">ASP.NET Core의 다음 부 버전은 새 `CompatibilityVersion` 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="d73fe-108">`CompatibilityVersion` 값 `Version_2_0`~`Version_2_2`은 `[Obsolete(...)]`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="d73fe-109">[위조 방지, CORS, 진단, Mvc 및 라우팅 내에서 주요 API 변경 내용](https://github.com/aspnet/Announcements/issues/387)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d73fe-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="d73fe-110">이 목록에는 호환성 스위치에 대한 주요 변경 내용이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="d73fe-111">`SetCompatibilityVersion`이 ASP.NET Core 2.x 앱과 어떻게 작동하는지 확인하려면 [이 문서의 ASP.NET Core 2.2 버전](?view=aspnetcore-2.2)을 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="d73fe-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d73fe-112"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드를 사용하면 ASP.NET Core MVC 2.1 또는 2.2에서 도입된 주요 동작 변경 내용을 ASP.NET Core 2.x 앱이 옵트인(opt-in) 또는 옵트아웃(opt-out)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="d73fe-113">이 주요 동작 변경 내용은 일반적으로 MVC 하위 시스템의 작동 방식과 런타임에서 **코드** 를 호출하는 방법과 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="d73fe-114">옵트인을 하면 ASP.NET Core의 최신 동작과 장기 동작을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="d73fe-115">다음 코드는 호환성 모드를 ASP.NET Core 2.2로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d73fe-116">최신 버전(`CompatibilityVersion.Latest`)을 사용하여 앱을 테스트하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="d73fe-117">대부분의 앱은 최신 버전을 사용하여 주요 동작을 변경하지 않을 것으로 예상합니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="d73fe-118">`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`을 호출하는 앱은 ASP.NET Core 2.1/2.2 MVC 버전에서 도입된 주요 동작 변경으로부터 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="d73fe-119">이 보호:</span><span class="sxs-lookup"><span data-stu-id="d73fe-119">This protection:</span></span>

* <span data-ttu-id="d73fe-120">2.1 이상의 모든 변경 내용에 적용되지는 않으며, MVC 하위 시스템의 주요 ASP.NET Core 런타임 동작 변경을 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="d73fe-121">ASP.NET Core 3.0으로 확장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="d73fe-122">`SetCompatibilityVersion`을 호출하지 **않는** ASP.NET Core 2.1 및 2.2 앱의 기본 호환성은 2.0 호환성입니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="d73fe-123">즉, `SetCompatibilityVersion`을 호출하지 않는 것은 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`을 호출하는 것과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="d73fe-124">다음 코드는 다음 동작을 제외하고, 호환성 모드를 ASP.NET Core 2.2로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="d73fe-125">적절한 호환성 스위치를 사용하여 주요 동작 변경 내용이 발생하는 앱의 경우:</span><span class="sxs-lookup"><span data-stu-id="d73fe-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="d73fe-126">최신 릴리스를 사용하고 특정 주요 동작 변경 내용을 옵트아웃(opt out)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="d73fe-127">최신 변경 내용과 함께 작동하도록 앱을 업데이트할 시간을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="d73fe-128"><xref:Microsoft.AspNetCore.Mvc.MvcOptions> 설명서에는 변경된 내용과 변경 내용이 대부분의 사용자에게 향상된 기능인 이유가 잘 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="d73fe-129">ASP.NET Core 3.0에서는 호환성 스위치가 지원하는 이전 동작이 제거되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="d73fe-130">거의 모든 사용자에게 혜택을 주는 긍정적인 변화라고 생각합니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="d73fe-131">이러한 변경 내용을 2.1 및 2.2에 도입하여 대부분의 앱은 혜택을 얻을 수 있는 반면, 다른 앱은 업데이트할 시간이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d73fe-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end