---
title: ASP.NET Core 메모리 내 캐시
author: rick-anderson
description: ASP.NET Core에서 데이터를 메모리에 캐시하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: 4d5f459d54a3c74a2eb23a50db6537eeaf8596b3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061446"
---
# <a name="cache-in-memory-in-aspnet-core"></a>ASP.NET Core 메모리 내 캐시

::: moniker range=">= aspnetcore-3.0"

작성자, [Rick Anderson](https://twitter.com/RickAndMSFT), [John 루 오 어](https://github.com/JunTaoLuo)및 [Steve Smith](https://ardalis.com/)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>캐싱 기본 사항

캐싱은 콘텐츠를 생성 하는 데 필요한 작업을 줄여 응용 프로그램의 성능과 확장성을 크게 향상 시킬 수 있습니다. 캐싱은 자주 변경 되지 **않으며** 생성 하는 데 비용이 많이 드는 데이터에서 가장 잘 작동 합니다. 캐싱은 원본에서 보다 훨씬 빠르게 반환 될 수 있는 데이터 복사본을 만듭니다. 캐시 된 데이터에 종속 **되지 않도록** 앱을 작성 하 고 테스트 해야 합니다.

ASP.NET Core는 여러 캐시를 지원 합니다. 가장 간단한 캐시는 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)을 기반으로 합니다. `IMemoryCache` 웹 서버의 메모리에 저장 된 캐시를 나타냅니다. 서버 팜 (여러 서버)에서 실행 되는 앱은 메모리 내 캐시를 사용할 때 세션이 고정 되어 있는지 확인 해야 합니다. 고정 세션은 클라이언트의 후속 요청이 모두 동일한 서버로 이동 하는지 확인 합니다. 예를 들어 Azure Web apps는 ARR ( [응용 프로그램 요청 라우팅](https://www.iis.net/learn/extensions/planning-for-arr) )을 사용 하 여 모든 후속 요청을 동일한 서버에 라우팅합니다.

웹 팜의 고정 되지 않은 세션은 캐시 일관성 문제를 방지 하기 위해 [분산 캐시가](distributed.md) 필요 합니다. 일부 앱의 경우 분산 캐시는 메모리 내 캐시 보다 더 높은 확장을 지원할 수 있습니다. 분산 캐시를 사용 하면 캐시 메모리가 외부 프로세스로 오프 로드 됩니다.

메모리 내 캐시는 모든 개체를 저장할 수 있습니다. 분산 캐시 인터페이스는로 제한 됩니다 `byte[]` . 메모리 내 및 분산 캐시 저장소는 키-값 쌍으로 항목을 캐시 합니다.

## <a name="systemruntimecachingmemorycache"></a>시스템 런타임 캐싱/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 패키지](https://www.nuget.org/packages/System.Runtime.Caching/))는와 함께 사용할 수 있습니다.

* 2.0 이상 .NET Standard 합니다.
* .NET Standard 2.0 이상을 대상으로 하는 [.net 구현](/dotnet/standard/net-standard#net-implementation-support) 예를 들어 ASP.NET Core 2.0 이상입니다.
* .NET Framework 4.5 이상

[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 이 문서에 설명 된 대로, `System.Runtime.Caching` / `MemoryCache` ASP.NET Core에 더 잘 통합 되기 때문에이 문서에 설명 되어 있는 것이 좋습니다. 예를 들어은 `IMemoryCache` ASP.NET Core [종속성 주입](xref:fundamentals/dependency-injection)을 기본적으로 사용 합니다.

`System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x에서 ASP.NET Core로 코드를 이식할 때 호환성 브리지로를 사용 합니다.

## <a name="cache-guidelines"></a>캐시 지침

* 코드에는 항상 데이터를 인출 하는 대체 (fallback) 옵션이 있으며 사용 가능한 캐시 된 값에 의존 **하지** 않아야 합니다.
* 캐시는 부족 한 리소스 인 메모리를 사용 합니다. 캐시 증가 제한:
  * 외부 입력을 캐시 키로 사용 **하지** 마십시오.
  * 캐시 증가를 제한 하려면 만료를 사용 합니다.
  * [SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기를 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)합니다. ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 **하지** 않습니다. 캐시 크기를 제한 하는 것은 개발자에 게 있습니다.

## <a name="use-imemorycache"></a>IMemoryCache 사용

> [!WARNING]
> [종속성 주입](xref:fundamentals/dependency-injection) 에서 *공유* 메모리 캐시를 사용 하 고, 또는를 호출 하 여 `SetSize` `Size` `SizeLimit` 캐시 크기를 제한 하면 앱에 오류가 발생할 수 있습니다. 캐시에 크기 제한이 설정 된 경우 모든 항목은 추가 될 때 크기를 지정 해야 합니다. 이로 인해 개발자가 공유 캐시를 사용 하는 항목을 완전히 제어할 수 없기 때문에 문제가 발생할 수 있습니다. 예를 들어 Entity Framework Core는 공유 캐시를 사용 하 고 크기를 지정 하지 않습니다. 앱이 캐시 크기 제한을 설정 하 고 EF Core를 사용 하는 경우 앱은을 throw `InvalidOperationException` 합니다.
> , 또는를 사용 하 여 `SetSize` `Size` `SizeLimit` 캐시를 제한 하는 경우 캐싱에 대 한 캐시 singleton을 만듭니다. 자세한 내용 및 예제는 [SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)을 참조 하세요.
> 공유 캐시는 다른 프레임 워크 또는 라이브러리에서 공유 하는 캐시입니다. 예를 들어 EF Core는 공유 캐시를 사용 하 고 크기를 지정 하지 않습니다. 

메모리 내 캐싱은 [종속성 주입](xref:fundamentals/dependency-injection)을 사용 하 여 앱에서 참조 되는 *서비스* 입니다. `IMemoryCache`생성자에서 인스턴스를 요청 합니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

다음 코드에서는 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) 를 사용 하 여 시간이 캐시에 있는지 확인 합니다. 시간이 캐시 되지 않으면 새 항목이 만들어지고 [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)을 사용 하 여 캐시에 추가 됩니다. `CacheKeys`클래스는 다운로드 샘플의 일부입니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

현재 시간과 캐시 된 시간이 표시 됩니다.

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

`DateTime`제한 시간 내에 요청이 있는 동안 캐시 된 값은 캐시에 남아 있습니다.

다음 코드는 [Getorcreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 및 [Getorcreateasync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 를 사용 하 여 데이터를 캐시 합니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

다음 코드는 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 을 호출 하 여 캐시 된 시간을 인출 합니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

다음 코드는 절대 만료를 사용 하는 캐시 된 항목을 가져오거나 만듭니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

슬라이딩 만료가 있는 캐시 된 항목 집합은 부실 해질 수 있습니다. 슬라이딩 만료 간격 보다 더 자주 액세스 하는 경우 항목은 만료 되지 않습니다. 절대 만료 시간이 지날 때 항목이 만료 되도록 하려면 슬라이딩 만료를 절대 만료와 결합 합니다. 절대 만료는 항목을 캐시할 수 있는 기간에 대 한 상한을 설정 하 고, 해당 항목이 슬라이딩 만료 간격 내에 요청 되지 않은 경우에도 이전에 만료 될 수 있도록 합니다. 절대 및 상대 (sliding) 만료가 모두 지정 된 경우 만료는 논리적으로 ORed 됩니다. 슬라이딩 만료 간격이 *나* 절대 만료 시간을 전달 하는 경우 항목은 캐시에서 제거 됩니다.

다음 코드는 슬라이딩 *및* 절대 만료를 모두 사용 하 여 캐시 된 항목을 가져오거나 만듭니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

위의 코드는 데이터가 절대 시간 보다 오래 캐시 되지 않도록 보장 합니다.

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 및 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> 는 클래스의 확장 메서드입니다 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> . 이러한 메서드는의 기능을 확장 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 합니다.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

다음 샘플입니다.

* 슬라이딩 만료 시간을 설정 합니다. 이 캐시 된 항목에 액세스 하는 요청은 슬라이딩 만료 시간을 다시 설정 합니다.
* 캐시 우선 순위를 [Cacheitempriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)로 설정 합니다.
* 항목이 캐시에서 제거 된 후에 호출 되는 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 을 설정 합니다. 콜백은 캐시에서 항목을 제거 하는 코드와 다른 스레드에서 실행 됩니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한

`MemoryCache`인스턴스는 필요에 따라 크기 제한을 지정 하 고 적용할 수 있습니다. 캐시 크기 제한에는 항목의 크기를 측정 하는 메커니즘이 없기 때문에 정의 된 측정 단위가 없습니다. 캐시 크기 제한이 설정 된 경우 모든 항목의 크기를 지정 해야 합니다. ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다. 캐시 크기를 제한 하는 것은 개발자에 게 있습니다. 지정 된 크기는 개발자가 선택 하는 단위입니다.

다음은 그 예입니다.

* 웹 앱이 주로 문자열을 캐싱하는 경우 각 캐시 엔트리 크기는 문자열 길이가 될 수 있습니다.
* 앱은 모든 항목의 크기를 1로 지정 하 고, 크기 제한은 항목 수를 지정 합니다.

<xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>가 설정 되지 않은 경우 캐시는 바인딩되지 않고 증가 합니다. 시스템 메모리가 부족할 때 ASP.NET Core 런타임은 캐시를 자르지 않습니다. 앱은 다음을 위해 설계 되어야 합니다.

* 캐시 증가를 제한 합니다.
* <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*>또는 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 사용 가능한 메모리가 제한 된 경우를 호출 합니다.

다음 코드는 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [종속성 주입](xref:fundamentals/dependency-injection)을 통해 액세스할 수 있는 고정 크기를 만듭니다.

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` 에 단위가 없습니다. 캐시 된 항목은 캐시 크기 한도가 설정 된 경우 가장 적합 한 모든 단위에서 크기를 지정 해야 합니다. 캐시 인스턴스의 모든 사용자는 동일한 단위 시스템을 사용 해야 합니다. 캐시 된 항목 크기의 합계가에 지정 된 값을 초과 하는 경우 항목이 캐시 되지 않습니다 `SizeLimit` . 캐시 크기 제한을 설정 하지 않으면 항목에 설정 된 캐시 크기가 무시 됩니다.

다음 코드는 `MyMemoryCache` [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록 됩니다.

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` 는이 크기 제한 된 캐시를 인식 하는 구성 요소에 대 한 독립 메모리 캐시로 만들어지며 캐시 엔트리 크기를 적절 하 게 설정 하는 방법을 알고 있습니다.

다음 코드에서는를 사용 합니다 `MyMemoryCache` .

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

캐시 엔트리의 크기는 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> 또는 확장 메서드를 통해 설정할 수 있습니다 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> .

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` 다음 순서에 따라 캐시의 지정 된 비율을 제거 하려고 시도 합니다.

* 만료 된 모든 항목입니다.
* 우선 순위별로 항목 우선 순위가 가장 낮은 항목이 먼저 제거 됩니다.
* 가장 최근에 사용한 개체입니다.
* 가장 빠른 절대 만료를 가진 항목입니다.
* 가장 빠른 슬라이딩 만료를 포함 하는 항목입니다.

우선 순위가 있는 고정 된 항목 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 은 제거 되지 않습니다. 다음 코드는 캐시 항목을 제거 하 고를 호출 합니다 `Compact` .

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

자세한 내용은 [GitHub의 Compact source](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) 를 참조 하세요.

## <a name="cache-dependencies"></a>캐시 종속성

다음 샘플에서는 종속 항목이 만료 된 경우 캐시 항목을 만료 하는 방법을 보여 줍니다. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>이 캐시 된 항목에 추가 됩니다. `Cancel`에 대해를 호출 하면 `CancellationTokenSource` 두 캐시 항목이 모두 제거 됩니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

를 사용 <xref:System.Threading.CancellationTokenSource> 하면 여러 캐시 항목을 하나의 그룹으로 제거할 수 있습니다. `using`위의 코드에서 패턴을 사용 하 여 블록 내에 만들어진 캐시 항목 `using` 은 트리거와 만료 설정을 상속 합니다.

## <a name="additional-notes"></a>추가적인 참고 사항

* 만료는 백그라운드에서 발생 하지 않습니다. 만료 된 항목에 대 한 캐시를 적극적으로 검색 하는 타이머가 없습니다. 캐시의 모든 작업 ( `Get` , `Set` ,)은 만료 된 `Remove` 항목에 대 한 백그라운드 검색을 트리거할 수 있습니다. `CancellationTokenSource`또한 ()의 타이머는 <xref:System.Threading.CancellationTokenSource.CancelAfter*> 항목을 제거 하 고 만료 된 항목에 대 한 검색을 트리거합니다. 다음 예에서는 등록 된 토큰에 대해 [CancellationTokenSource (TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) 을 사용 합니다. 이 토큰이 발생 하면 엔트리를 즉시 제거 하 고 제거 콜백을 발생 시킵니다.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* 콜백을 사용 하 여 캐시 항목을 다시 채우는 경우:

  * 콜백이 완료 되지 않아 여러 요청에서 캐시 된 키 값이 비어 있는 것을 찾을 수 있습니다.
  * 이로 인해 여러 스레드가 캐시 된 항목을 늘이기 수 있습니다.

* 한 캐시 엔트리를 사용 하 여 다른 캐시 엔트리를 만드는 경우 자식는 부모 항목의 만료 토큰 및 시간 기반 만료 설정을 복사 합니다. 자식은 수동으로 제거 하거나 부모 항목을 업데이트 하 여 만료 되지 않습니다.

* 캐시 <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> 엔트리를 캐시에서 제거한 후에 발생 하는 콜백을 설정 하는 데 사용 됩니다.
* 대부분의 앱에서 `IMemoryCache` 이 사용 됩니다. 예를 들어,,, `AddMvc` `AddControllersWithViews` `AddRazorPages` `AddMvcCore().AddRazorViewEngine` 및의 다른 여러 메서드를 호출 하면 `Add{Service}` `ConfigureServices` 가 활성화 `IMemoryCache` 됩니다. 이전 메서드 중 하나를 호출 하지 않는 앱의 경우 `Add{Service}` 에서를 호출 해야 할 수 있습니다 <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> `ConfigureServices` .

## <a name="background-cache-update"></a>백그라운드 캐시 업데이트

과 같은 [백그라운드 서비스](xref:fundamentals/host/hosted-services) 를 사용 <xref:Microsoft.Extensions.Hosting.IHostedService> 하 여 캐시를 업데이트 합니다. 백그라운드 서비스는 항목을 다시 계산 하 고 준비 된 경우에만 캐시에 할당할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
작성자, [Rick Anderson](https://twitter.com/RickAndMSFT), [John 루 오 어](https://github.com/JunTaoLuo)및 [Steve Smith](https://ardalis.com/)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>캐싱 기본 사항

캐싱은 콘텐츠를 생성 하는 데 필요한 작업을 줄여 응용 프로그램의 성능과 확장성을 크게 향상 시킬 수 있습니다. 캐싱은 자주 변경 되지 않는 데이터에 가장 적합 합니다. 캐싱은 원래 원본에서 보다 훨씬 빠르게 반환 될 수 있는 데이터 복사본을 만듭니다. 캐시 된 데이터에 종속 **되지 않도록** 코드를 작성 하 고 테스트 해야 합니다.

ASP.NET Core는 여러 캐시를 지원 합니다. 가장 간단한 캐시는 웹 서버의 메모리에 저장 된 캐시를 나타내는 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)을 기반으로 합니다. 서버 팜 (여러 서버)에서 실행 되는 앱은 메모리 내 캐시를 사용할 때 세션이 고정 되어 있는지 확인 해야 합니다. 고정 세션은 클라이언트의 이후 요청이 모두 동일한 서버로 이동 하는지 확인 합니다. 예를 들어 Azure Web apps는 ARR ( [응용 프로그램 요청 라우팅](https://www.iis.net/learn/extensions/planning-for-arr) )을 사용 하 여 사용자 에이전트의 모든 요청을 동일한 서버로 라우팅합니다.

웹 팜의 고정 되지 않은 세션은 캐시 일관성 문제를 방지 하기 위해 [분산 캐시가](distributed.md) 필요 합니다. 일부 앱의 경우 분산 캐시는 메모리 내 캐시 보다 더 높은 확장을 지원할 수 있습니다. 분산 캐시를 사용 하면 캐시 메모리가 외부 프로세스로 오프 로드 됩니다.

메모리 내 캐시는 모든 개체를 저장할 수 있습니다. 분산 캐시 인터페이스는로 제한 됩니다 `byte[]` . 메모리 내 및 분산 캐시 저장소는 키-값 쌍으로 항목을 캐시 합니다.

## <a name="systemruntimecachingmemorycache"></a>시스템 런타임 캐싱/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 패키지](https://www.nuget.org/packages/System.Runtime.Caching/))는와 함께 사용할 수 있습니다.

* 2.0 이상 .NET Standard 합니다.
* .NET Standard 2.0 이상을 대상으로 하는 [.net 구현](/dotnet/standard/net-standard#net-implementation-support) 예를 들어 ASP.NET Core 2.0 이상입니다.
* .NET Framework 4.5 이상

[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 이 문서에 설명 된 대로, `System.Runtime.Caching` / `MemoryCache` ASP.NET Core에 더 잘 통합 되기 때문에이 문서에 설명 되어 있는 것이 좋습니다. 예를 들어은 `IMemoryCache` ASP.NET Core [종속성 주입](xref:fundamentals/dependency-injection)을 기본적으로 사용 합니다.

`System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x에서 ASP.NET Core로 코드를 이식할 때 호환성 브리지로를 사용 합니다.

## <a name="cache-guidelines"></a>캐시 지침

* 코드에는 항상 데이터를 인출 하는 대체 (fallback) 옵션이 있으며 사용 가능한 캐시 된 값에 의존 **하지** 않아야 합니다.
* 캐시는 부족 한 리소스 인 메모리를 사용 합니다. 캐시 증가 제한:
  * 외부 입력을 캐시 키로 사용 **하지** 마십시오.
  * 캐시 증가를 제한 하려면 만료를 사용 합니다.
  * [SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기를 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)합니다. ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다. 캐시 크기를 제한 하는 것은 개발자에 게 있습니다.

## <a name="using-imemorycache"></a>IMemoryCache 사용

> [!WARNING]
> [종속성 주입](xref:fundamentals/dependency-injection) 에서 *공유* 메모리 캐시를 사용 하 고, 또는를 호출 하 여 `SetSize` `Size` `SizeLimit` 캐시 크기를 제한 하면 앱에 오류가 발생할 수 있습니다. 캐시에 크기 제한이 설정 된 경우 모든 항목은 추가 될 때 크기를 지정 해야 합니다. 이로 인해 개발자가 공유 캐시를 사용 하는 항목을 완전히 제어할 수 없기 때문에 문제가 발생할 수 있습니다. 예를 들어 Entity Framework Core는 공유 캐시를 사용 하 고 크기를 지정 하지 않습니다. 앱이 캐시 크기 제한을 설정 하 고 EF Core를 사용 하는 경우 앱은을 throw `InvalidOperationException` 합니다.
> , 또는를 사용 하 여 `SetSize` `Size` `SizeLimit` 캐시를 제한 하는 경우 캐싱에 대 한 캐시 singleton을 만듭니다. 자세한 내용 및 예제는 [SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한](#use-setsize-size-and-sizelimit-to-limit-cache-size)을 참조 하세요.

메모리 내 캐싱은 [종속성 주입](../../fundamentals/dependency-injection.md)을 사용 하 여 앱에서 참조 되는 *서비스* 입니다. `AddMemoryCache`에서 호출 `ConfigureServices` :

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

`IMemoryCache`생성자에서 인스턴스를 요청 합니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache`[AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에서 사용할 수 있는 [NuGet 패키지를](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)설치 해야 합니다.

다음 코드에서는 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) 를 사용 하 여 시간이 캐시에 있는지 확인 합니다. 시간이 캐시 되지 않으면 새 항목이 만들어지고 [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)을 사용 하 여 캐시에 추가 됩니다.

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

현재 시간과 캐시 된 시간이 표시 됩니다.

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

`DateTime`제한 시간 내에 요청이 있는 동안 캐시 된 값은 캐시에 남아 있습니다. 다음 이미지는 현재 시간과 캐시에서 검색 된 이전 시간을 보여 줍니다.

![서로 다른 두 시간이 표시 된 인덱스 뷰](memory/_static/time.png)

다음 코드는 [Getorcreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 및 [Getorcreateasync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 를 사용 하 여 데이터를 캐시 합니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

다음 코드는 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 을 호출 하 여 캐시 된 시간을 인출 합니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 및 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 은의 기능을 확장 하는 [cacheextensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 클래스의 일부인 확장 메서드입니다 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . 다른 캐시 메서드에 대 한 설명은 [IMemoryCache 메서드](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) 및 [cacheextensions 메서드](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 를 참조 하세요.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

다음 샘플입니다.

* 슬라이딩 만료 시간을 설정 합니다. 이 캐시 된 항목에 액세스 하는 요청은 슬라이딩 만료 시간을 다시 설정 합니다.
* 캐시 우선 순위를로 설정 합니다 `CacheItemPriority.NeverRemove` .
* 항목이 캐시에서 제거 된 후에 호출 되는 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 을 설정 합니다. 콜백은 캐시에서 항목을 제거 하는 코드와 다른 스레드에서 실행 됩니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>SetSize, Size 및 SizeLimit를 사용 하 여 캐시 크기 제한

`MemoryCache`인스턴스는 필요에 따라 크기 제한을 지정 하 고 적용할 수 있습니다. 캐시 크기 제한에는 항목의 크기를 측정 하는 메커니즘이 없기 때문에 정의 된 측정 단위가 없습니다. 캐시 크기 제한이 설정 된 경우 모든 항목의 크기를 지정 해야 합니다. ASP.NET Core 런타임은 메모리 압력에 따라 캐시 크기를 제한 하지 않습니다. 캐시 크기를 제한 하는 것은 개발자에 게 있습니다. 지정 된 크기는 개발자가 선택 하는 단위입니다.

다음은 그 예입니다.

* 웹 앱이 주로 문자열을 캐싱하는 경우 각 캐시 엔트리 크기는 문자열 길이가 될 수 있습니다.
* 앱은 모든 항목의 크기를 1로 지정 하 고, 크기 제한은 항목 수를 지정 합니다.

<xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>가 설정 되지 않은 경우 캐시는 바인딩되지 않고 증가 합니다. 시스템 메모리가 부족할 때 ASP.NET Core 런타임은 캐시를 자르지 않습니다. 앱은 다음에 맞게 설계 되었습니다.

* 캐시 증가를 제한 합니다.
* <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*>또는 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 사용 가능한 메모리가 제한 된 경우를 호출 합니다.

다음 코드는 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [종속성 주입](xref:fundamentals/dependency-injection)을 통해 액세스할 수 있는 고정 크기를 만듭니다.

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` 에 단위가 없습니다. 캐시 된 항목은 캐시 크기 한도가 설정 된 경우 가장 적합 한 모든 단위에서 크기를 지정 해야 합니다. 캐시 인스턴스의 모든 사용자는 동일한 단위 시스템을 사용 해야 합니다. 캐시 된 항목 크기의 합계가에 지정 된 값을 초과 하는 경우 항목이 캐시 되지 않습니다 `SizeLimit` . 캐시 크기 제한을 설정 하지 않으면 항목에 설정 된 캐시 크기가 무시 됩니다.

다음 코드는 `MyMemoryCache` [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 등록 됩니다.

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` 는이 크기 제한 된 캐시를 인식 하는 구성 요소에 대 한 독립 메모리 캐시로 만들어지며 캐시 엔트리 크기를 적절 하 게 설정 하는 방법을 알고 있습니다.

다음 코드에서는를 사용 합니다 `MyMemoryCache` .

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

캐시 엔트리의 크기는 [크기](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) 또는 [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) 확장 메서드를 통해 설정할 수 있습니다.

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` 다음 순서에 따라 캐시의 지정 된 비율을 제거 하려고 시도 합니다.

* 만료 된 모든 항목입니다.
* 우선 순위별로 항목 우선 순위가 가장 낮은 항목이 먼저 제거 됩니다.
* 가장 최근에 사용한 개체입니다.
* 가장 빠른 절대 만료를 가진 항목입니다.
* 가장 빠른 슬라이딩 만료를 포함 하는 항목입니다.

우선 순위가 있는 고정 된 항목 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 은 제거 되지 않습니다.

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

자세한 내용은 [GitHub의 Compact source](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) 를 참조 하세요.

## <a name="cache-dependencies"></a>캐시 종속성

다음 샘플에서는 종속 항목이 만료 된 경우 캐시 항목을 만료 하는 방법을 보여 줍니다. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>이 캐시 된 항목에 추가 됩니다. `Cancel`에 대해를 호출 하면 `CancellationTokenSource` 두 캐시 항목이 모두 제거 됩니다.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

를 사용 `CancellationTokenSource` 하면 여러 캐시 항목을 하나의 그룹으로 제거할 수 있습니다. `using`위의 코드에서 패턴을 사용 하 여 블록 내에 만들어진 캐시 항목 `using` 은 트리거와 만료 설정을 상속 합니다.

## <a name="additional-notes"></a>추가적인 참고 사항

* 콜백을 사용 하 여 캐시 항목을 다시 채우는 경우:

  * 콜백이 완료 되지 않아 여러 요청에서 캐시 된 키 값이 비어 있는 것을 찾을 수 있습니다.
  * 이로 인해 여러 스레드가 캐시 된 항목을 늘이기 수 있습니다.

* 한 캐시 엔트리를 사용 하 여 다른 캐시 엔트리를 만드는 경우 자식는 부모 항목의 만료 토큰 및 시간 기반 만료 설정을 복사 합니다. 자식은 수동으로 제거 하거나 부모 항목을 업데이트 하 여 만료 되지 않습니다.

* [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) 를 사용 하 여 캐시 엔트리를 캐시에서 제거한 후에 발생 하는 콜백을 설정 합니다.

## <a name="background-cache-update"></a>백그라운드 캐시 업데이트

과 같은 [백그라운드 서비스](xref:fundamentals/host/hosted-services) 를 사용 <xref:Microsoft.Extensions.Hosting.IHostedService> 하 여 캐시를 업데이트 합니다. 백그라운드 서비스는 항목을 다시 계산 하 고 준비 된 경우에만 캐시에 할당할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
