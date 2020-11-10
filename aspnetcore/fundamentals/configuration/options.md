---
title: ASP.NET Core의 옵션 패턴
author: rick-anderson
description: 옵션 패턴을 사용하여 ASP.NET Core 앱에서 관련된 설정 그룹을 나타내는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061095"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="e2485-103">ASP.NET Core의 옵션 패턴</span><span class="sxs-lookup"><span data-stu-id="e2485-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2485-104">작성자: [Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT).</span><span class="sxs-lookup"><span data-stu-id="e2485-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="e2485-105">옵션 패턴은 클래스를 사용하여 관련 설정 그룹에 대한 강력한 형식의 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="e2485-106">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="e2485-107">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="e2485-108">[문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="e2485-109">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="e2485-110">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="e2485-111">이 항목에서는 ASP.NET Core의 옵션 패턴 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="e2485-112">콘솔 앱에서 옵션 패턴을 사용하는 방법에 관한 자세한 내용은 [.NET의 옵션 패턴](/dotnet/core/extensions/options)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="e2485-113">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2485-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="e2485-114">계층적 구성 바인딩</span><span class="sxs-lookup"><span data-stu-id="e2485-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="e2485-115">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="e2485-115">Options interfaces</span></span>

<span data-ttu-id="e2485-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="e2485-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="e2485-117">지원하지 **않음** : 앱이 시작된 후 구성 데이터 읽기.</span><span class="sxs-lookup"><span data-stu-id="e2485-117">Does \* **not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="e2485-118">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="e2485-118">Named options</span></span>](#named)
* <span data-ttu-id="e2485-119">[Singleton](xref:fundamentals/dependency-injection#singleton)으로 등록되며 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="e2485-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="e2485-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="e2485-121">모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="e2485-122">자세한 내용은 [IOptionsSnapshot을 사용하여 업데이트된 데이터](#ios)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="e2485-123">[범위 지정됨](xref:fundamentals/dependency-injection#scoped)으로 등록되므로 Singleton 서비스에 주입할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="e2485-124">[명명된 옵션](#named)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-124">Supports [named options](#named)</span></span>

<span data-ttu-id="e2485-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="e2485-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="e2485-126">옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="e2485-127">[Singleton](xref:fundamentals/dependency-injection#singleton)으로 등록되며 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)에 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="e2485-128">지원:</span><span class="sxs-lookup"><span data-stu-id="e2485-128">Supports:</span></span>
  * <span data-ttu-id="e2485-129">변경 알림</span><span class="sxs-lookup"><span data-stu-id="e2485-129">Change notifications</span></span>
  * [<span data-ttu-id="e2485-130">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="e2485-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="e2485-131">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="e2485-132">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="e2485-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="e2485-133">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 발생한 후에 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="e2485-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="e2485-135">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="e2485-136">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="e2485-137"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="e2485-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="e2485-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="e2485-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="e2485-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="e2485-141">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="e2485-142">IOptionsSnapshot을 사용하여 업데이트된 데이터 읽기</span><span class="sxs-lookup"><span data-stu-id="e2485-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="e2485-143"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하면, 옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="e2485-144">업데이트된 구성 값 읽기를 지원하는 구성 공급자를 사용하는 경우 앱을 시작한 후 구성 변경 내용을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="e2485-145">`IOptionsMonitor`와 `IOptionsSnapshot`의 차이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="e2485-146">`IOptionsMonitor`는 언제든지 현재 옵션 값을 검색하는 [싱글톤 서비스](xref:fundamentals/dependency-injection#singleton)로, 싱글톤 종속성에서 특히 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="e2485-147">`IOptionsSnapshot`은 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#scoped)이며 `IOptionsSnapshot<T>` 개체가 생성될 때 옵션의 스냅샷을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="e2485-148">옵션 스냅숏은 임시 및 범위가 지정된 종속성과 함께 사용하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="e2485-149">다음 코드에서는 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="e2485-150">다음 코드에서는 `MyOptions`가 바인딩되는 구성 인스턴스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="e2485-151">위 코드에서는 앱을 시작한 후 JSON 구성 파일 변경 사항을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="e2485-152">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="e2485-152">IOptionsMonitor</span></span>

<span data-ttu-id="e2485-153">다음 코드는 `MyOptions`가 바인딩되는 구성 인스턴스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="e2485-154">다음 예제에서는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="e2485-155">위 코드에서는 기본적으로 앱을 시작한 후의 JSON 구성 파일의 변경 사항을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="e2485-156">명명된 옵션은 IConfigureNamedOptions 사용을 지원</span><span class="sxs-lookup"><span data-stu-id="e2485-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="e2485-157">명명된 옵션:</span><span class="sxs-lookup"><span data-stu-id="e2485-157">Named options:</span></span>

* <span data-ttu-id="e2485-158">여러 구성 섹션이 동일한 속성에 바인딩되는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="e2485-159">대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-159">Are case sensitive.</span></span>

<span data-ttu-id="e2485-160">다음 *:::no-loc(appsettings.json):::* 파일을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-160">Consider the following *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="e2485-161">`TopItem:Month` 및 `TopItem:Year`를 바인딩하는 두 개의 클래스를 만드는 대신 각 섹션에 다음 클래스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="e2485-162">다음 코드는 명명된 옵션을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="e2485-163">다음 코드는 명명된 옵션을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="e2485-164">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-164">All options are named instances.</span></span> <span data-ttu-id="e2485-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="e2485-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="e2485-167"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="e2485-168">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="e2485-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="e2485-170">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="e2485-170">OptionsBuilder API</span></span>

<span data-ttu-id="e2485-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="e2485-172">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="e2485-173">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="e2485-174">`OptionsBuilder`는 [옵션 유효성 검사](#val) 섹션에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="e2485-175">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-175">Use DI services to configure options</span></span>

<span data-ttu-id="e2485-176">다음 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입에서 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="e2485-177">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="e2485-178">`OptionsBuilder<TOptions>`는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="e2485-179"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="e2485-180">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="e2485-181">형식을 만드는 작업은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 호출할 때 프레임워크에서 수행하는 작업에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="e2485-182">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="e2485-183">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="e2485-183">Options validation</span></span>

<span data-ttu-id="e2485-184">옵션 유효성 검사를 통해 옵션 값의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="e2485-185">다음 *:::no-loc(appsettings.json):::* 파일을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-185">Consider the following *:::no-loc(appsettings.json):::* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="e2485-186">다음 클래스는 `"MyConfig"` 구성 섹션에 바인딩되고 몇 가지 `DataAnnotations` 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="e2485-187">코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-187">The following code:</span></span>

* <span data-ttu-id="e2485-188"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>를 호출하여 `MyConfigOptions` 클래스에 바인딩되는 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="e2485-189"><xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A>를 호출하여 `DataAnnotations`를 사용한 유효성 검사를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="e2485-190">`ValidateDataAnnotations` 확장 메서드는 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet 패키지에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="e2485-191">`Microsoft.NET.Sdk.Web` SDK를 사용하는 웹앱의 경우 이 패키지는 공유 프레임워크에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="e2485-192">다음 코드는 구성 값 또는 유효성 검사 오류를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="e2485-193">다음 코드는 대리자를 사용하여 보다 복잡한 유효성 검사 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="e2485-194">복잡한 유효성 검사를 위한 IValidateOptions</span><span class="sxs-lookup"><span data-stu-id="e2485-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="e2485-195">다음 클래스는 <xref:Microsoft.Extensions.Options.IValidateOptions`1>를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="e2485-196">`IValidateOptions`를 사용하면 유효성 검사 코드를 `StartUp`에서 클래스로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="e2485-197">위의 코드에서는 다음 코드를 사용하여 `Startup.ConfigureServices`에서 유효성 검사를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="e2485-198">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-198">Options post-configuration</span></span>

<span data-ttu-id="e2485-199"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="e2485-200">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2485-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2485-202">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="e2485-203">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="e2485-203">Accessing options during startup</span></span>

<span data-ttu-id="e2485-204">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="e2485-205">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e2485-206">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="e2485-207">Options.ConfigurationExtensions NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="e2485-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="e2485-208">[Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지는 ASP.NET Core 앱에서 암시적으로 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e2485-209">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="e2485-210">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="e2485-211">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="e2485-212">[문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="e2485-213">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="e2485-214">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="e2485-215">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2485-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2485-216">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="e2485-216">Prerequisites</span></span>

<span data-ttu-id="e2485-217">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="e2485-218">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="e2485-218">Options interfaces</span></span>

<span data-ttu-id="e2485-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="e2485-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="e2485-221">변경 알림</span><span class="sxs-lookup"><span data-stu-id="e2485-221">Change notifications</span></span>
* [<span data-ttu-id="e2485-222">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="e2485-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="e2485-223">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="e2485-224">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="e2485-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="e2485-225">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="e2485-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="e2485-227">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="e2485-228">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="e2485-229"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="e2485-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="e2485-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="e2485-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="e2485-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="e2485-233">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="e2485-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="e2485-235">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="e2485-236"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="e2485-237">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="e2485-238"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptions%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="e2485-239">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-239">General options configuration</span></span>

<span data-ttu-id="e2485-240">일반 옵션 구성은 샘플 앱에 예제 1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="e2485-241">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="e2485-242">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="e2485-243">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="e2485-244">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다( *Models/MyOptions.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-244">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="e2485-245">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="e2485-246">다음 페이지 모델은 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 통해 [생성자 종속성 주입](xref:mvc/controllers/dependency-injection)을 사용하여 설정에 액세스합니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="e2485-247">샘플의 *:::no-loc(appsettings.json):::* 파일은 `option1` 및 `option2`의 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-247">The sample's *:::no-loc(appsettings.json):::* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/:::no-loc(appsettings.json):::?highlight=2-3)]

<span data-ttu-id="e2485-248">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="e2485-249">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile(":::no-loc(appsettings.json):::", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="e2485-250"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="e2485-251">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="e2485-252">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="e2485-253">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-253">Use a delegate to set options values.</span></span> <span data-ttu-id="e2485-254">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스( *Models/MyOptionsWithDelegateConfig.cs* )를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-254">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="e2485-255">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="e2485-256">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="e2485-257">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="e2485-257">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="e2485-258">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="e2485-259">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="e2485-260">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="e2485-261"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="e2485-262">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *:::no-loc(appsettings.json):::* 에서 지정되지만 `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *:::no-loc(appsettings.json):::* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="e2485-263">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리* 하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="e2485-264">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="e2485-265">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-265">Suboptions configuration</span></span>

<span data-ttu-id="e2485-266">하위 옵션 구성은 샘플 앱에 예제 3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="e2485-267">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="e2485-268">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="e2485-269">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="e2485-270">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 *:::no-loc(appsettings.json):::* 의 `option1` 속성에서 읽힙니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *:::no-loc(appsettings.json):::*.</span></span>

<span data-ttu-id="e2485-271">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="e2485-272">이 서비스는 `MySubOptions`를 *:::no-loc(appsettings.json):::* 파일의 섹션 `subsection`에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-272">It binds `MySubOptions` to the section `subsection` of the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="e2485-273">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="e2485-274">샘플의 *:::no-loc(appsettings.json):::* 파일은 `suboption1` 및 `suboption2`에 대한 키로 `subsection` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-274">The sample's *:::no-loc(appsettings.json):::* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/:::no-loc(appsettings.json):::?highlight=4-7)]

<span data-ttu-id="e2485-275">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다( *Models/MySubOptions.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="e2485-276">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-276">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="e2485-277">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="e2485-278">옵션 삽입</span><span class="sxs-lookup"><span data-stu-id="e2485-278">Options injection</span></span>

<span data-ttu-id="e2485-279">옵션 삽입은 샘플 앱에 예제 4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="e2485-280">다음에 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="e2485-281">[`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: 지시문이 포함된 :::no-loc(Razor)::: 페이지 또는 MVC 뷰.</span><span class="sxs-lookup"><span data-stu-id="e2485-281">A :::no-loc(Razor)::: page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive.</span></span>
* <span data-ttu-id="e2485-282">페이지 또는 뷰 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-282">A page or view model.</span></span>

<span data-ttu-id="e2485-283">샘플 앱의 다음 예제는 페이지 모델( *Pages/Index.cshtml.cs* )에 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="e2485-284">샘플 앱은 `@inject` 지시문을 사용하여 `IOptionsMonitor<MyOptions>`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="e2485-285">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-285">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="e2485-287">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="e2485-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="e2485-288"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="e2485-289"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하면, 옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="e2485-290">`IOptionsMonitor`와 `IOptionsSnapshot`의 차이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="e2485-291">`IOptionsMonitor`는 언제든지 현재 옵션 값을 검색하는 [싱글톤 서비스](xref:fundamentals/dependency-injection#singleton)로, 싱글톤 종속성에서 특히 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="e2485-292">`IOptionsSnapshot`은 [범위가 지정된 서비스](xref:fundamentals/dependency-injection#scoped)이며 `IOptionsSnapshot<T>` 개체가 생성될 때 옵션의 스냅샷을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="e2485-293">옵션 스냅숏은 임시 및 범위가 지정된 종속성과 함께 사용하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="e2485-294">다음 예제에는 *:::no-loc(appsettings.json):::* 이 변경된 후 새 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 만드는 방법이 설명되어 있습니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *:::no-loc(appsettings.json):::* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="e2485-295">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *:::no-loc(appsettings.json):::* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-295">Multiple requests to the server return constant values provided by the *:::no-loc(appsettings.json):::* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="e2485-296">다음 이미지는 *:::no-loc(appsettings.json):::* 파일에서 로드된 초기 `option1` 및 `option2` 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-296">The following image shows the initial `option1` and `option2` values loaded from the *:::no-loc(appsettings.json):::* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="e2485-297">*:::no-loc(appsettings.json):::* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-297">Change the values in the *:::no-loc(appsettings.json):::* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="e2485-298">*:::no-loc(appsettings.json):::* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-298">Save the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="e2485-299">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="e2485-300">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="e2485-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="e2485-301"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="e2485-302">앱은 명명된 옵션 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="e2485-303">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 확장 메서드를 호출하는 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="e2485-304">명명된 옵션은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="e2485-305">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="e2485-306">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="e2485-307">`named_options_1` 값은 *:::no-loc(appsettings.json):::* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-307">`named_options_1` values are provided from configuration, which are loaded from the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="e2485-308">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="e2485-309">`Option1`에 대한 `ConfigureServices`의 `named_options_2` 대리자.</span><span class="sxs-lookup"><span data-stu-id="e2485-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="e2485-310">`MyOptions` 클래스에서 제공되는 `Option2`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="e2485-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="e2485-311">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="e2485-312"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="e2485-313">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="e2485-314">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="e2485-315">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="e2485-316">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-316">All options are named instances.</span></span> <span data-ttu-id="e2485-317">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="e2485-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="e2485-319"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="e2485-320">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="e2485-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="e2485-321">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="e2485-321">OptionsBuilder API</span></span>

<span data-ttu-id="e2485-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="e2485-323">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="e2485-324">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="e2485-325">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-325">Use DI services to configure options</span></span>

<span data-ttu-id="e2485-326">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="e2485-327">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="e2485-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="e2485-329"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="e2485-330">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="e2485-331">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="e2485-332">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="e2485-333">옵션 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="e2485-333">Options validation</span></span>

<span data-ttu-id="e2485-334">옵션 유효성 검사를 사용하면 옵션이 구성될 때 옵션의 유효성을 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="e2485-335">옵션이 유효하면 `true`를 반환하고 옵션이 유효하지 않으면 `false`를 반환하는 유효성 검사 메서드로 `Validate`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="e2485-336">위의 예제에서는 명명된 옵션 인스턴스를 `optionalOptionsName`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="e2485-337">기본 옵션 인스턴스는 `Options.DefaultName`입니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="e2485-338">유효성 검사는 옵션 인스턴스가 만들어지면 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="e2485-339">옵션 인스턴스는 처음 액세스되면 유효성 검사를 통과하도록 보장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e2485-340">옵션 유효성 검사는 옵션 인스턴스가 생성된 후 옵션 수정에 대해 보호하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="e2485-341">예를 들어 옵션이 처음 액세스될 때 요청마다 한 번씩 `IOptionsSnapshot` 옵션이 생성되고 유효성이 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="e2485-342">`IOptionsSnapshot` 옵션은 *동일한 요청에 대한* 다음 액세스 시도에서 다시 검사되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="e2485-343">`Validate` 메서드는 `Func<TOptions, bool>`을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="e2485-344">유효성 검사를 완전히 사용자 지정하려면 다음을 허용하는 `IValidateOptions<TOptions>`를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="e2485-345">여러 옵션 형식의 유효성 검사: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="e2485-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="e2485-346">다른 옵션 형식에 의존하는 유효성 검사: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="e2485-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="e2485-347">`IValidateOptions`는 다음의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="e2485-348">특정 명명된 옵션 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="e2485-348">A specific named options instance.</span></span>
* <span data-ttu-id="e2485-349">`name`이 `null`인 경우 모든 옵션.</span><span class="sxs-lookup"><span data-stu-id="e2485-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="e2485-350">인터페이스의 구현에서 `ValidateOptionsResult`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="e2485-351">데이터 주석 기반 유효성 검사는 `OptionsBuilder<TOptions>`의 <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> 메서드를 호출하여 [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="e2485-352">`Microsoft.Extensions.Options.DataAnnotations`는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="e2485-353">즉시 유효성 검사(시작 시 빠른 실패)는 향후 릴리스에서 고려 중입니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="e2485-354">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-354">Options post-configuration</span></span>

<span data-ttu-id="e2485-355"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="e2485-356">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2485-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2485-358">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="e2485-359">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="e2485-359">Accessing options during startup</span></span>

<span data-ttu-id="e2485-360">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="e2485-361">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e2485-362">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="e2485-363">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="e2485-364">[구성 설정](xref:fundamentals/configuration/index)이 시나리오에 따라 별도 클래스로 격리된 경우 앱은 두 가지 중요한 소프트웨어 엔지니어링 원칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="e2485-365">[ISP(Interface Segregation Principle, 인터페이스 분리 원칙) 또는 캡슐화](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): 구성 설정에 종속된 시나리오(클래스)는 사용하는 구성 설정에 의해서만 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="e2485-366">[문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) 앱의 다른 부분에 대한 설정은 다른 설정에 종속되거나 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="e2485-367">옵션은 구성 데이터의 유효성을 검사하는 메커니즘도 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="e2485-368">자세한 내용은 [옵션 유효성 검사](#options-validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="e2485-369">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2485-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e2485-370">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="e2485-370">Prerequisites</span></span>

<span data-ttu-id="e2485-371">[Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조하거나 [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) 패키지에 대한 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="e2485-372">옵션 인터페이스</span><span class="sxs-lookup"><span data-stu-id="e2485-372">Options interfaces</span></span>

<span data-ttu-id="e2485-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>는 옵션을 검색하고 `TOptions` 인스턴스에 대한 옵션 알림을 관리하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="e2485-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>은 다음 시나리오를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="e2485-375">변경 알림</span><span class="sxs-lookup"><span data-stu-id="e2485-375">Change notifications</span></span>
* [<span data-ttu-id="e2485-376">명명된 옵션</span><span class="sxs-lookup"><span data-stu-id="e2485-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="e2485-377">다시 로드할 수 있는 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="e2485-378">선택적 옵션 무효화(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="e2485-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="e2485-379">[사후 구성](#options-post-configuration) 시나리오에서는 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후 옵션을 설정하거나 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="e2485-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>는 새로운 옵션 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="e2485-381">단일 <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="e2485-382">기본 구현에서는 등록된 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 및 <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하며 먼저 구성을 모두 실행한 다음, 사후 구성을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="e2485-383"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>와 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>를 구별하며 적절한 인터페이스만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="e2485-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 `TOptions` 인스턴스를 캐시하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="e2485-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>는 모니터의 옵션 인스턴스를 무효화하므로 값이 다시 계산됩니다(<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="e2485-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="e2485-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>를 사용하여 값을 수동으로 도입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="e2485-387">모든 명명된 인스턴스를 필요에 따라 다시 만들어야 하는 경우 <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="e2485-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 모든 요청에서 옵션을 다시 계산해야 하는 시나리오에서 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="e2485-389">자세한 내용은 [IOptionsSnapshot을 사용하여 구성 데이터 다시 로드](#reload-configuration-data-with-ioptionssnapshot)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="e2485-390"><xref:Microsoft.Extensions.Options.IOptions%601>를 사용하여 옵션을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="e2485-391">그러나 <xref:Microsoft.Extensions.Options.IOptions%601>는 앞에서 설명한 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>의 시나리오를 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="e2485-392"><xref:Microsoft.Extensions.Options.IOptions%601> 인터페이스를 이미 사용하고 있으며 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>에서 제공하는 시나리오가 필요하지 않은 기존 프레임워크 및 라이브러리에서는 <xref:Microsoft.Extensions.Options.IOptions%601>를 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="e2485-393">일반 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-393">General options configuration</span></span>

<span data-ttu-id="e2485-394">일반 옵션 구성은 샘플 앱에 예제 1로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="e2485-395">옵션 클래스는 매개 변수가 없는 public 생성자를 사용하는 비추상이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="e2485-396">다음 클래스 `MyOptions`에는 `Option1` 및 `Option2`의 두 가지 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="e2485-397">기본 값 설정은 선택 사항이지만, 다음 예제의 클래스 생성자는 `Option1`의 기본값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="e2485-398">`Option2`에는 직접 속성을 초기화하여 설정된 기본값이 있습니다( *Models/MyOptions.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-398">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="e2485-399">`MyOptions` 클래스는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="e2485-400">다음 페이지 모델은 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 통해 [생성자 종속성 주입](xref:mvc/controllers/dependency-injection)을 사용하여 설정에 액세스합니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="e2485-401">샘플의 *:::no-loc(appsettings.json):::* 파일은 `option1` 및 `option2`의 값을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-401">The sample's *:::no-loc(appsettings.json):::* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/:::no-loc(appsettings.json):::?highlight=2-3)]

<span data-ttu-id="e2485-402">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="e2485-403">사용자 지정 <xref:System.Configuration.ConfigurationBuilder>를 사용하여 설정 파일에서 옵션 구성을 로드하는 경우 기본 경로가 올바르게 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile(":::no-loc(appsettings.json):::", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="e2485-404"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 통해 설정 파일에서 옵션 구성을 로드하는 경우에는 명시적으로 기본 경로를 설정하지 않아도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="e2485-405">대리자로 간단한 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="e2485-406">대리자를 사용한 간단한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="e2485-407">대리자를 사용하여 옵션 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-407">Use a delegate to set options values.</span></span> <span data-ttu-id="e2485-408">샘플 앱에서는 `MyOptionsWithDelegateConfig` 클래스( *Models/MyOptionsWithDelegateConfig.cs* )를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-408">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="e2485-409">다음 코드에서 두 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="e2485-410">`MyOptionsWithDelegateConfig`를 통해 바인딩을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="e2485-411">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="e2485-411">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="e2485-412">여러 구성 공급자를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="e2485-413">구성 공급자는 NuGet 패키지에서 사용할 수 있으며 등록된 순서대로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="e2485-414">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="e2485-415"><xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*>를 호출할 때마다 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="e2485-416">위의 예제에서는 `Option1` 및 `Option2`의 값은 모두 *:::no-loc(appsettings.json):::* 에서 지정되지만 `Option1` 및 `Option2`의 값은 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *:::no-loc(appsettings.json):::* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="e2485-417">둘 이상의 구성 서비스를 활성화한 경우 마지막 지정된 구성 소스가 *승리* 하고 구성 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="e2485-418">앱을 실행할 때 페이지 모델의 `OnGet` 메서드는 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="e2485-419">하위 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-419">Suboptions configuration</span></span>

<span data-ttu-id="e2485-420">하위 옵션 구성은 샘플 앱에 예제 3으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="e2485-421">앱은 앱의 특정 시나리오 그룹(클래스)에 적합한 옵션 클래스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="e2485-422">구성 값을 필요로 하는 앱의 부분은 사용하는 구성 값에만 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="e2485-423">옵션을 구성에 바인딩하는 경우 옵션 형식의 각 속성은 `property[:sub-property:]` 양식의 구성 키에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="e2485-424">예를 들어 `MyOptions.Option1` 속성은 키 `Option1`에 바인딩되어 *:::no-loc(appsettings.json):::* 의 `option1` 속성에서 읽힙니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *:::no-loc(appsettings.json):::*.</span></span>

<span data-ttu-id="e2485-425">다음 코드에서 세 번째 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="e2485-426">이 서비스는 `MySubOptions`를 *:::no-loc(appsettings.json):::* 파일의 섹션 `subsection`에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-426">It binds `MySubOptions` to the section `subsection` of the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="e2485-427">`GetSection` 메서드를 사용하려면 <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> 네임스페이스가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="e2485-428">샘플의 *:::no-loc(appsettings.json):::* 파일은 `suboption1` 및 `suboption2`에 대한 키로 `subsection` 멤버를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-428">The sample's *:::no-loc(appsettings.json):::* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/:::no-loc(appsettings.json):::?highlight=4-7)]

<span data-ttu-id="e2485-429">`MySubOptions` 클래스는 `SubOption1` 및 `SubOption2` 속성을 정의하여 옵션 값을 유지합니다( *Models/MySubOptions.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="e2485-430">페이지 모델의 `OnGet` 메서드는 옵션 값이 포함된 문자열을 반환합니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-430">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="e2485-431">앱을 실행할 때 `OnGet` 메서드는 하위 옵션 클래스 값을 표시하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="e2485-432">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션</span><span class="sxs-lookup"><span data-stu-id="e2485-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="e2485-433">직접 보기 주입 또는 보기 모델에 의해 제공되는 옵션은 샘플 앱에 예제 4로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="e2485-434">옵션은 뷰 모델 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 보기에 직접 주입하여 제공할 수 있습니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="e2485-435">샘플 앱은 `@inject` 지시문을 사용하여 `IOptionsMonitor<MyOptions>`를 주입하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="e2485-436">앱을 실행하면 옵션 값이 렌더링된 페이지에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-436">When the app is run, the options values are shown in the rendered page:</span></span>

![옵션 값 Option1: value1_from_json 및 Option2: -1은 모델에서 로드되며 보기에 주입됩니다.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="e2485-438">IOptionsSnapshot을 사용하여 구성 데이터 다시 로드</span><span class="sxs-lookup"><span data-stu-id="e2485-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="e2485-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 사용하여 구성 데이터를 다시 로드하는 방법은 샘플 앱에 예제 5로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="e2485-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>은 최소한의 처리 오버헤드로 옵션 다시 로드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="e2485-441">옵션은 엑세스될 때 요청 당 한 번씩 계산되고 요청의 수명 동안 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="e2485-442">다음 예제에는 *:::no-loc(appsettings.json):::* 이 변경된 후 새 <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>을 만드는 방법이 설명되어 있습니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *:::no-loc(appsettings.json):::* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="e2485-443">서버에 대한 여러 요청은 파일이 변경되고 구성이 다시 로드될 때까지 *:::no-loc(appsettings.json):::* 파일에서 제공하는 상수 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-443">Multiple requests to the server return constant values provided by the *:::no-loc(appsettings.json):::* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="e2485-444">다음 이미지는 *:::no-loc(appsettings.json):::* 파일에서 로드된 초기 `option1` 및 `option2` 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-444">The following image shows the initial `option1` and `option2` values loaded from the *:::no-loc(appsettings.json):::* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="e2485-445">*:::no-loc(appsettings.json):::* 파일의 값을 `value1_from_json UPDATED` 및 `200`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-445">Change the values in the *:::no-loc(appsettings.json):::* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="e2485-446">*:::no-loc(appsettings.json):::* 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-446">Save the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="e2485-447">옵션 값이 변경되었음을 확인하려면 브라우저를 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="e2485-448">IConfigureNamedOptions로 명명된 옵션 지원</span><span class="sxs-lookup"><span data-stu-id="e2485-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="e2485-449"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>로 명명된 옵션 지원은 샘플 앱에 예제 6으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="e2485-450">앱은 명명된 옵션 지원을 통해 명명된 옵션 구성들을 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="e2485-451">샘플 앱에서 명명된 옵션은 [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) 확장 메서드를 호출하는 [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*)를 사용하여 선언됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="e2485-452">명명된 옵션은 대/소문자를 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="e2485-453">샘플 앱은 <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*>을 사용하여 명명된 옵션에 액세스합니다( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2485-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="e2485-454">샘플 앱을 실행하면 명명된 옵션이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="e2485-455">`named_options_1` 값은 *:::no-loc(appsettings.json):::* 파일에서 로드되는 구성에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-455">`named_options_1` values are provided from configuration, which are loaded from the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="e2485-456">`named_options_2` 값은 다음에서 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="e2485-457">`Option1`에 대한 `ConfigureServices`의 `named_options_2` 대리자.</span><span class="sxs-lookup"><span data-stu-id="e2485-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="e2485-458">`MyOptions` 클래스에서 제공되는 `Option2`에 대한 기본값.</span><span class="sxs-lookup"><span data-stu-id="e2485-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="e2485-459">ConfigureAll 메서드를 사용하여 모든 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="e2485-460"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 메서드를 사용하여 모든 옵션 인스턴스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="e2485-461">다음 코드는 모든 구성 인스턴스에 대해 `Option1`을 공통 값으로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="e2485-462">다음 코드를 `Startup.ConfigureServices` 메서드에 직접 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="e2485-463">코드를 추가한 후 샘플 앱을 실행하면 다음과 같은 결과가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="e2485-464">모든 옵션은 명명된 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-464">All options are named instances.</span></span> <span data-ttu-id="e2485-465">기존 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 인스턴스는 `string.Empty`인 `Options.DefaultName` 인스턴스를 대상으로 지정한 것처럼 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="e2485-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>는 <xref:Microsoft.Extensions.Options.IConfigureOptions%601>도 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="e2485-467"><xref:Microsoft.Extensions.Options.IOptionsFactory%601>의 기본 구현에는 각 옵션을 적절하게 사용하기 위한 논리가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="e2485-468">`null` 명명된 옵션은 특정 명명된 인스턴스 대신 모든 명명된 인스턴스를 대상으로 지정하는 데 사용됩니다(<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> 및 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>에서 이 규칙을 사용함).</span><span class="sxs-lookup"><span data-stu-id="e2485-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="e2485-469">OptionsBuilder API</span><span class="sxs-lookup"><span data-stu-id="e2485-469">OptionsBuilder API</span></span>

<span data-ttu-id="e2485-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601>는 `TOptions` 인스턴스를 구성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="e2485-471">`OptionsBuilder`는 `AddOptions<TOptions>(string optionsName)` 호출에 대한 단일 매개 변수이므로 모든 후속 호출에 나타나는 대신 명명된 옵션 생성을 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="e2485-472">옵션 유효성 검사 및 서비스 종속성을 허용하는 `ConfigureOptions` 오버로드는 `OptionsBuilder`를 통해서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="e2485-473">DI 서비스를 사용하여 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-473">Use DI services to configure options</span></span>

<span data-ttu-id="e2485-474">다음과 같은 두 가지 방법으로 옵션을 구성하는 동안 종속성 주입을 통해 다른 서비스에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="e2485-475">구성 대리자를 [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)의 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="e2485-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)는 최대 5개의 서비스를 사용하여 옵션을 구성할 수 있는 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)의 오버로드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="e2485-477"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> 또는 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>를 구현하는 고유의 형식을 만들고 해당 형식을 서비스로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="e2485-478">서비스를 만드는 것이 더 복잡하기 때문에 구성 대리자를 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)에 전달하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="e2485-479">고유의 형식을 만드는 것은 [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)를 사용할 때 프레임워크가 수행하는 것과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="e2485-480">[Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)을 호출하면 지정된 일반 서비스 유형을 허용하는 생성자가 있는 일시적인 일반 <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="e2485-481">옵션 사후 구성</span><span class="sxs-lookup"><span data-stu-id="e2485-481">Options post-configuration</span></span>

<span data-ttu-id="e2485-482"><xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>를 사용하여 사후 구성을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="e2485-483">사후 구성은 모든 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 구성이 수행된 후에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2485-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>를 사용하여 명명된 옵션을 사후 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="e2485-485">모든 구성 인스턴스를 사후 구성하려면 <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*>을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="e2485-486">시작하는 동안 옵션 액세스</span><span class="sxs-lookup"><span data-stu-id="e2485-486">Accessing options during startup</span></span>

<span data-ttu-id="e2485-487">`Configure` 메서드가 실행되기 전에 서비스가 만들어지므로 `Startup.Configure`에서 <xref:Microsoft.Extensions.Options.IOptions%601> 및 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="e2485-488">`Startup.ConfigureServices`에서는 <xref:Microsoft.Extensions.Options.IOptions%601> 또는 <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>를 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e2485-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e2485-489">서비스 등록의 순서 지정으로 인해 일관성 없는 옵션 상태가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e2485-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e2485-490">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e2485-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
