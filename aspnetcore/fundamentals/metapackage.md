---
title: ASP.NET Core 2.0용 Microsoft.AspNetCore.All 메타패키지
author: Rick-Anderson
description: ASP.NET Core 2.1 이상은 Microsoft.AspNetCore.All 메타패키지를 사용하는 것이 좋습니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
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
uid: fundamentals/metapackage
ms.openlocfilehash: b739398c2a440f21c8bdfdc1f4d8e25412358a6a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060692"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a><span data-ttu-id="4a5b9-103">ASP.NET Core 2.0용 Microsoft.AspNetCore.All 메타패키지</span><span class="sxs-lookup"><span data-stu-id="4a5b9-103">Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4a5b9-104">`Microsoft.AspNetCore.All` 메타패키지는 ASP.NET Core 3.0 이상에 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-104">The `Microsoft.AspNetCore.All` metapackage isn't included in ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="4a5b9-105">자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/Announcements/issues/314)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-105">For more information, see [this GitHub issue](https://github.com/aspnet/Announcements/issues/314).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="4a5b9-106">ASP.NET Core 2.1 이상을 대상으로 하는 애플리케이션은 이 패키지가 아닌 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-106">We recommend applications targeting ASP.NET Core 2.1 and later use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) rather than this package.</span></span> <span data-ttu-id="4a5b9-107">이 문서의 [Microsoft.AspNetCore.All에서 Microsoft.AspNetCore.App으로 마이그레이션](#migrate)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-107">See [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](#migrate) in this article.</span></span>

<span data-ttu-id="4a5b9-108">이 기능을 사용하려면 .NET Core 2.x를 대상으로 하는 ASP.NET Core 2.x가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-108">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="4a5b9-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All)은 공유 프레임워크를 참조하는 메타패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-109">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) is a metapackage that refers to a shared framework.</span></span> <span data-ttu-id="4a5b9-110">공유 프레임워크는 앱 폴더에 없는 어셈블리( *.dll* 파일) 세트입니다. </span><span class="sxs-lookup"><span data-stu-id="4a5b9-110">A *shared framework* is a set of assemblies ( *.dll* files) that are not in the app's folders.</span></span> <span data-ttu-id="4a5b9-111">공유 프레임워크는 앱을 실행할 머신에 설치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-111">The shared framework must be installed on the machine to run the app.</span></span> <span data-ttu-id="4a5b9-112">자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-112">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="4a5b9-113">`Microsoft.AspNetCore.All`이 참조하는 공유 프레임워크에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-113">The shared framework that `Microsoft.AspNetCore.All` refers to includes:</span></span>

* <span data-ttu-id="4a5b9-114">ASP.NET Core 팀에서 지원되는 모든 패키지</span><span class="sxs-lookup"><span data-stu-id="4a5b9-114">All supported packages by the ASP.NET Core team.</span></span>
* <span data-ttu-id="4a5b9-115">Entity Framework Core에서 지원되는 모든 패키지</span><span class="sxs-lookup"><span data-stu-id="4a5b9-115">All supported packages by the Entity Framework Core.</span></span>
* <span data-ttu-id="4a5b9-116">ASP.NET Core 및 Entity Framework Core에서 사용되는 내부 및 타사 종속성</span><span class="sxs-lookup"><span data-stu-id="4a5b9-116">Internal and 3rd-party dependencies used by ASP.NET Core and Entity Framework Core.</span></span>

<span data-ttu-id="4a5b9-117">ASP.NET Core 2.x 및 Entity Framework Core 2.x의 모든 기능은 `Microsoft.AspNetCore.All` 패키지에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-117">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.All` package.</span></span> <span data-ttu-id="4a5b9-118">ASP.NET Core 2.0을 대상으로 하는 기본 프로젝트 템플릿에는 이 패키지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-118">The default project templates targeting ASP.NET Core 2.0 use this package.</span></span>

<span data-ttu-id="4a5b9-119">`Microsoft.AspNetCore.All` 메타패키지의 버전 번호는 최소 ASP.NET Core 버전 및 Entity Framework Core 버전을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-119">The version number of the `Microsoft.AspNetCore.All` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="4a5b9-120">다음 *.csproj* 파일은 ASP.NET Core용 `Microsoft.AspNetCore.All` 메타패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-120">The following *.csproj* file references the `Microsoft.AspNetCore.All` metapackage for ASP.NET Core:</span></span>

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a><span data-ttu-id="4a5b9-121">암시적 버전 관리</span><span class="sxs-lookup"><span data-stu-id="4a5b9-121">Implicit versioning</span></span>

<span data-ttu-id="4a5b9-122">ASP.NET Core 2.1 이상에서는 버전 없이 `Microsoft.AspNetCore.All` 패키지 참조를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-122">In ASP.NET Core 2.1 or later, you can specify the `Microsoft.AspNetCore.All` package reference without a version.</span></span> <span data-ttu-id="4a5b9-123">버전이 지정되지 않은 경우 SDK(`Microsoft.NET.Sdk.Web`)에 의해 암시적 버전이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-123">When the version isn't specified, an implicit version is specified by the SDK (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="4a5b9-124">SDK에서 지정하는 암시적 버전을 사용하고, 패키지 참조에 버전 번호를 명시적으로 설정하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-124">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="4a5b9-125">이 방법에 대한 질문이 있는 경우 GitHub의 [Microsoft.AspNetCore.App 암시적 버전에 대한 토론](https://github.com/dotnet/AspNetCore.Docs/issues/6430)에 의견을 남겨 주세요.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-125">If you have questions about this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="4a5b9-126">휴대용 앱의 암시적 버전은 `major.minor.0`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-126">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="4a5b9-127">공유 프레임워크 롤포워드 메커니즘은 설치된 공유 프레임워크 중 최신 호환 버전에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-127">The shared framework roll-forward mechanism runs the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="4a5b9-128">개발, 테스트 및 프로덕션에서 동일한 버전이 사용되도록 하려면 모든 환경에 동일한 버전의 공유 프레임워크를 설치하도록 하세요.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-128">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="4a5b9-129">자체 포함 앱의 경우 암시적 버전 번호가 설치된 SDK에 포함된 공유 프레임워크의 `major.minor.patch`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-129">For self-contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="4a5b9-130">`Microsoft.AspNetCore.All` 패키지 참조에 버전 번호를 지정해도 해당 버전의 고유 프레임워크가 선택된다고 보장할 수 **없습니다**.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-130">Specifying a version number on the `Microsoft.AspNetCore.All` package reference does **not** guarantee that version of the shared framework is chosen.</span></span> <span data-ttu-id="4a5b9-131">예를 들어 "2.1.1" 버전을 지정했는데 "2.1.3"이 설치되는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-131">For example, suppose version "2.1.1" is specified, but "2.1.3" is installed.</span></span> <span data-ttu-id="4a5b9-132">이 경우 앱은 "2.1.3"을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-132">In that case, the app will use "2.1.3".</span></span> <span data-ttu-id="4a5b9-133">권장되는 방법은 아니지만 롤포워드를 비활성화할 수 있습니다(패치 및/또는 부 버전).</span><span class="sxs-lookup"><span data-stu-id="4a5b9-133">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="4a5b9-134">DotNet 호스트 롤포워드 및 이 동작을 구성하는 방법에 대한 자세한 내용은 [DotNet 호스트 롤포워드](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-134">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

<span data-ttu-id="4a5b9-135">`Microsoft.AspNetCore.All`의 암시적 버전을 사용하려면 프로젝트의 SDK를 프로젝트 파일의 `Microsoft.NET.Sdk.Web`으로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-135">The project's SDK must be set to `Microsoft.NET.Sdk.Web` in the project file to use the implicit version of `Microsoft.AspNetCore.All`.</span></span> <span data-ttu-id="4a5b9-136">`Microsoft.NET.Sdk` SDK를 지정하면(프로젝트 파일의 맨 위에 있는 `<Project Sdk="Microsoft.NET.Sdk">`) 다음 경고가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-136">When the `Microsoft.NET.Sdk` SDK is specified (`<Project Sdk="Microsoft.NET.Sdk">` at the top of the project file), the following warning is generated:</span></span>

<span data-ttu-id="4a5b9-137">*경고 NU1604: 프로젝트 종속성 Microsoft.AspNetCore.All에 포함 하한이 포함되어 있지 않습니다. 일관된 복원 결과를 얻으려면 종속 버전의 하한을 포함하세요.*</span><span class="sxs-lookup"><span data-stu-id="4a5b9-137">*Warning NU1604: Project dependency Microsoft.AspNetCore.All does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

<span data-ttu-id="4a5b9-138">이는 .NET Core 2.1 SDK의 알려진 문제이며 .NET Core 2.2 SDK에서 수정될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-138">This is a known issue with the .NET Core 2.1 SDK and will be fixed in the .NET Core 2.2 SDK.</span></span>

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a><span data-ttu-id="4a5b9-139">Microsoft.AspNetCore.All에서 Microsoft.AspNetCore.App으로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="4a5b9-139">Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App</span></span>

<span data-ttu-id="4a5b9-140">`Microsoft.AspNetCore.All`에는 다음 패키지가 포함되어 있지만 `Microsoft.AspNetCore.App` 패키지는 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-140">The following packages are included in `Microsoft.AspNetCore.All` but not the `Microsoft.AspNetCore.App` package.</span></span>

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

<span data-ttu-id="4a5b9-141">앱이 이전 패키지 또는 이러한 패키지에서 가져온 패키지의 API를 사용하는 경우 `Microsoft.AspNetCore.All`에서 `Microsoft.AspNetCore.App`으로 전환하려면 프로젝트에 이러한 패키지에 대한 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-141">To move from `Microsoft.AspNetCore.All` to `Microsoft.AspNetCore.App`, if your app uses any APIs from the above packages, or packages brought in by those packages, add references to those packages in your project.</span></span>

<span data-ttu-id="4a5b9-142">`Microsoft.AspNetCore.App`의 종속성이 아닌 이전 패키지의 종속성은 암시적으로 포함되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-142">Any dependencies of the preceding packages that otherwise aren't dependencies of `Microsoft.AspNetCore.App` are not included implicitly.</span></span> <span data-ttu-id="4a5b9-143">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="4a5b9-143">For example:</span></span>

* <span data-ttu-id="4a5b9-144">`Microsoft.Extensions.Caching.Redis`의 종속성 `StackExchange.Redis`</span><span class="sxs-lookup"><span data-stu-id="4a5b9-144">`StackExchange.Redis` as a dependency of `Microsoft.Extensions.Caching.Redis`</span></span>
* <span data-ttu-id="4a5b9-145">`Microsoft.AspNetCore.ApplicationInsights.HostingStartup`의 종속성 `Microsoft.ApplicationInsights`</span><span class="sxs-lookup"><span data-stu-id="4a5b9-145">`Microsoft.ApplicationInsights` as a dependency of `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`</span></span>

## <a name="update-aspnet-core-21"></a><span data-ttu-id="4a5b9-146">ASP.NET Core 2.1 업데이트</span><span class="sxs-lookup"><span data-stu-id="4a5b9-146">Update ASP.NET Core 2.1</span></span>

<span data-ttu-id="4a5b9-147">2\.1 이상의 경우 `Microsoft.AspNetCore.App` 메타패키지로 마이그레이션하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-147">We recommend migrating to the `Microsoft.AspNetCore.App` metapackage for 2.1 and later.</span></span> <span data-ttu-id="4a5b9-148">`Microsoft.AspNetCore.All` 메타패키지를 계속 사용하고 최신 패치 버전이 배포되었는지 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="4a5b9-148">To keep using the `Microsoft.AspNetCore.All` metapackage and ensure the latest patch version is deployed:</span></span>

* <span data-ttu-id="4a5b9-149">개발 머신 및 빌드 서버: 최신 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-149">On development machines and build servers: Install the latest [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="4a5b9-150">배포 서버: 최신 [.NET Core 런타임](https://dotnet.microsoft.com/download)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-150">On deployment servers: Install the latest [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>
 <span data-ttu-id="4a5b9-151">애플리케이션을 다시 시작하면 앱이 최신 설치 버전으로 롤포워드됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a5b9-151">Your app will roll forward to the latest installed version on an application restart.</span></span>
