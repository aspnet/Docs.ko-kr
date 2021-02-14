---
title: ASP.NET Core 분산 캐싱
author: rick-anderson
description: 특히 클라우드 또는 서버 팜 환경에서 ASP.NET Core 분산 캐시를 사용 하 여 앱 성능 및 확장성을 개선 하는 방법에 대해 알아봅니다.
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
uid: performance/caching/distributed
ms.openlocfilehash: 6f89046f2e1805111dd81b3282253a72a7c6ea09
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281021"
---
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core 분산 캐싱

작성자- [Mohsin Nasir](https://github.com/mohsinnasir) 및 [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다. 분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.

분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.

캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.

* 는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.
* 서버를 다시 시작 하 고 앱을 배포 합니다.
* 로컬 메모리를 사용 하지 않습니다.

분산 캐시 구성은 구현 별로 다릅니다. 이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다. 선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

SQL Server 분산 캐시를 사용 하려면 패키지 참조를 [Microsoft 확장명이](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) . i n t. i n t. i n t 패키지에 추가 합니다.

Redis 분산 캐시를 사용 하려면 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 패키지 참조를 추가 합니다.

NCache 분산 캐시를 사용 하려면 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 패키지에 대 한 패키지 참조를 추가 합니다.

## <a name="idistributedcache-interface"></a>IDistributedCache 인터페이스

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.

## <a name="establish-distributed-caching-services"></a>분산 캐싱 서비스 설정

에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다. 이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.

* [분산 메모리 캐시](#distributed-memory-cache)
* [분산 SQL Server 캐시](#distributed-sql-server-cache)
* [Distributed Redis cache](#distributed-redis-cache)
* [Distributed NCache cache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>분산 메모리 캐시

분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다. 분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다. 캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.

분산 메모리 캐시는 다음과 같은 유용한 구현입니다.

* 개발 및 테스트 시나리오에서
* 프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다. 분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다. 이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.

샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>분산 SQL Server 캐시

분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다. SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` . 이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.

명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` . SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

도구가 성공 했음을 나타내는 메시지가 기록 됩니다.

```console
Table and index were created successfully.
```

도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 appsettings에 의해 저장 됨) *appsettings.json* / *. 환경}. json* 파일). 연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.

### <a name="distributed-redis-cache"></a>분산 Redis Cache

[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.  Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성 하 고 로컬 개발에 Azure Redis Cache를 사용할 수 있습니다.

앱은 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> .

자세한 내용은 [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)를 참조하세요.

로컬 Redis cache에 대 한 대체 접근 방식에 대 한 설명은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/19542) 를 참조 하세요.

### <a name="distributed-ncache-cache"></a>Distributed NCache Cache

[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다. NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.

로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [Windows 용 시작 가이드 (.net 및 .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)를 참조 하세요.

NCache를 구성 하려면:

1. [NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.
1. [Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.
1. 다음 코드를 `Startup.ConfigureServices`에 추가합니다.

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>분산 캐시 사용

인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다. 인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.

샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` . 현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 자세한 내용은 [제네릭 호스트: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)를 참조 하세요.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.

인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` . 캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다. 캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료* 가 표시 됩니다.

캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다. 단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> 인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).
>
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.

## <a name="recommendations"></a>권장 사항

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.

* 기존 인프라
* 성능 요구 사항
* 비용
* 팀 환경

캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다. 일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.

일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다. 그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.

SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다. 분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.

## <a name="additional-resources"></a>추가 자료

* [Azure의 Redis Cache](/azure/azure-cache-for-redis/)
* [Azure의 SQL Database](/azure/sql-database/)
* [웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다. 분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.

분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.

캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.

* 는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.
* 서버를 다시 시작 하 고 앱을 배포 합니다.
* 로컬 메모리를 사용 하지 않습니다.

분산 캐시 구성은 구현 별로 다릅니다. 이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다. 선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 추가 합니다. Redis 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.

NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다.. NCache 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 NCache 패키지를 참조 해야 합니다.

## <a name="idistributedcache-interface"></a>IDistributedCache 인터페이스

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.

## <a name="establish-distributed-caching-services"></a>분산 캐싱 서비스 설정

에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다. 이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.

* [분산 메모리 캐시](#distributed-memory-cache)
* [분산 SQL Server 캐시](#distributed-sql-server-cache)
* [Distributed Redis cache](#distributed-redis-cache)
* [Distributed NCache cache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>분산 메모리 캐시

분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다. 분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다. 캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.

분산 메모리 캐시는 다음과 같은 유용한 구현입니다.

* 개발 및 테스트 시나리오에서
* 프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다. 분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다. 이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.

샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>분산 SQL Server 캐시

분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다. SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` . 이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.

명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` . SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

도구가 성공 했음을 나타내는 메시지가 기록 됩니다.

```console
Table and index were created successfully.
```

도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 appsettings에 의해 저장 됨) *appsettings.json* / *. 환경}. json* 파일). 연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.

### <a name="distributed-redis-cache"></a>분산 Redis Cache

[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다. Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.

앱은 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 다음과 같은 개발 이외의 환경에서 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 `Startup.ConfigureServices` .

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.

1. [Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.
1. `redis-server`명령 프롬프트에서를 실행 합니다.

### <a name="distributed-ncache-cache"></a>Distributed NCache Cache

[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다. NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.

로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [Windows 용 시작 가이드 (.net 및 .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)를 참조 하세요.

NCache를 구성 하려면:

1. [NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.
1. [Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.
1. 다음 코드를 `Startup.ConfigureServices`에 추가합니다.

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>분산 캐시 사용

인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다. 인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.

샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` . 현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.

인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` . 캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다. 캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료* 가 표시 됩니다.

캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다. 단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> 인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).
>
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.

## <a name="recommendations"></a>권장 사항

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.

* 기존 인프라
* 성능 요구 사항
* 비용
* 팀 환경

캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다. 일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.

일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다. 그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.

SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다. 분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.

## <a name="additional-resources"></a>추가 자료

* [Azure의 Redis Cache](/azure/azure-cache-for-redis/)
* [Azure의 SQL Database](/azure/sql-database/)
* [웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다. 분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.

분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.

캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.

* 는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.
* 서버를 다시 시작 하 고 앱을 배포 합니다.
* 로컬 메모리를 사용 하지 않습니다.

분산 캐시 구성은 구현 별로 다릅니다. 이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다. 선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 패키지에 추가 합니다. Redis 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.

NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다.. NCache 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 NCache 패키지를 참조 해야 합니다.

## <a name="idistributedcache-interface"></a>IDistributedCache 인터페이스

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.

## <a name="establish-distributed-caching-services"></a>분산 캐싱 서비스 설정

에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다. 이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.

* [분산 메모리 캐시](#distributed-memory-cache)
* [분산 SQL Server 캐시](#distributed-sql-server-cache)
* [Distributed Redis cache](#distributed-redis-cache)
* [Distributed NCache cache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>분산 메모리 캐시

분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다. 분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다. 캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.

분산 메모리 캐시는 다음과 같은 유용한 구현입니다.

* 개발 및 테스트 시나리오에서
* 프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다. 분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다. 이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.

샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>분산 SQL Server 캐시

분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다. SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` . 이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.

명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` . SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

도구가 성공 했음을 나타내는 메시지가 기록 됩니다.

```console
Table and index were created successfully.
```

도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 appsettings에 의해 저장 됨) *appsettings.json* / *. 환경}. json* 파일). 연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.

### <a name="distributed-redis-cache"></a>분산 Redis Cache

[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다. Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.

앱은 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> .

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.

1. [Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.
1. `redis-server`명령 프롬프트에서를 실행 합니다.

### <a name="distributed-ncache-cache"></a>Distributed NCache Cache

[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다. NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.

로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [Windows 용 시작 가이드 (.net 및 .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)를 참조 하세요.

NCache를 구성 하려면:

1. [NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.
1. [Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.
1. 다음 코드를 `Startup.ConfigureServices`에 추가합니다.

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>분산 캐시 사용

인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다. 인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.

샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` . 현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.

인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` . 캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다. 캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료* 가 표시 됩니다.

캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다. 단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> 인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).
>
> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.

## <a name="recommendations"></a>권장 사항

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.

* 기존 인프라
* 성능 요구 사항
* 비용
* 팀 환경

캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다. 일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.

일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다. 그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.

SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다. 분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.

## <a name="additional-resources"></a>추가 자료

* [Azure의 Redis Cache](/azure/azure-cache-for-redis/)
* [Azure의 SQL Database](/azure/sql-database/)
* [웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
