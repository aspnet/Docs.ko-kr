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
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="5de4b-103">ASP.NET Core 분산 캐싱</span><span class="sxs-lookup"><span data-stu-id="5de4b-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="5de4b-104">작성자- [Mohsin Nasir](https://github.com/mohsinnasir) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5de4b-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5de4b-105">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="5de4b-106">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="5de4b-107">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="5de4b-108">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="5de4b-109">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="5de4b-110">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="5de4b-111">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-111">Doesn't use local memory.</span></span>

<span data-ttu-id="5de4b-112">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="5de4b-113">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="5de4b-114">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="5de4b-115">선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="5de4b-116">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5de4b-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5de4b-117">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-117">Prerequisites</span></span>

<span data-ttu-id="5de4b-118">SQL Server 분산 캐시를 사용 하려면 패키지 참조를 [Microsoft 확장명이](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) . i n t. i n t. i n t 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="5de4b-119">Redis 분산 캐시를 사용 하려면 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="5de4b-120">NCache 분산 캐시를 사용 하려면 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 패키지에 대 한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="5de4b-121">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="5de4b-121">IDistributedCache interface</span></span>

<span data-ttu-id="5de4b-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="5de4b-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="5de4b-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="5de4b-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="5de4b-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="5de4b-127">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="5de4b-127">Establish distributed caching services</span></span>

<span data-ttu-id="5de4b-128">에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5de4b-129">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="5de4b-130">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="5de4b-131">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="5de4b-132">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="5de4b-133">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="5de4b-134">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-134">Distributed Memory Cache</span></span>

<span data-ttu-id="5de4b-135">분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="5de4b-136">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="5de4b-137">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="5de4b-138">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="5de4b-139">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="5de4b-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="5de4b-140">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="5de4b-141">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="5de4b-142">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="5de4b-143">샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="5de4b-144">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="5de4b-145">분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="5de4b-146">SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="5de4b-147">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="5de4b-148">명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="5de4b-149">SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="5de4b-150">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="5de4b-151">도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="5de4b-153">앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="5de4b-154">샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="5de4b-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 appsettings에 의해 저장 됨) *appsettings.json* / *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="5de4b-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="5de4b-156">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="5de4b-157">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-157">Distributed Redis Cache</span></span>

<span data-ttu-id="5de4b-158">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span>  <span data-ttu-id="5de4b-159">Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성 하 고 로컬 개발에 Azure Redis Cache를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-159">You can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app, and use an Azure Redis Cache for local development.</span></span>

<span data-ttu-id="5de4b-160">앱은 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>).</span></span>

<span data-ttu-id="5de4b-161">자세한 내용은 [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-161">For more information, see [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span></span>

<span data-ttu-id="5de4b-162">로컬 Redis cache에 대 한 대체 접근 방식에 대 한 설명은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/19542) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-162">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/19542) for a discussion on alternative approaches to a local Redis cache.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="5de4b-163">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-163">Distributed NCache Cache</span></span>

<span data-ttu-id="5de4b-164">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-164">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="5de4b-165">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-165">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="5de4b-166">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [Windows 용 시작 가이드 (.net 및 .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-166">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="5de4b-167">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="5de4b-167">To configure NCache:</span></span>

1. <span data-ttu-id="5de4b-168">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-168">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="5de4b-169">[Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-169">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="5de4b-170">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-170">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="5de4b-171">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="5de4b-171">Use the distributed cache</span></span>

<span data-ttu-id="5de4b-172">인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-172">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="5de4b-173">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-173">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5de4b-174">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-174">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="5de4b-175">현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 자세한 내용은 [제네릭 호스트: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-175">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="5de4b-176">샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-176">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="5de4b-177">인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-177">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="5de4b-178">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-178">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="5de4b-179">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료* 가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-179">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="5de4b-180">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-180">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="5de4b-181">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-181">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="5de4b-182">인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="5de4b-182">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="5de4b-183"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-183">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="5de4b-184">권장 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-184">Recommendations</span></span>

<span data-ttu-id="5de4b-185"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-185">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="5de4b-186">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="5de4b-186">Existing infrastructure</span></span>
* <span data-ttu-id="5de4b-187">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-187">Performance requirements</span></span>
* <span data-ttu-id="5de4b-188">비용</span><span class="sxs-lookup"><span data-stu-id="5de4b-188">Cost</span></span>
* <span data-ttu-id="5de4b-189">팀 환경</span><span class="sxs-lookup"><span data-stu-id="5de4b-189">Team experience</span></span>

<span data-ttu-id="5de4b-190">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-190">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="5de4b-191">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-191">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="5de4b-192">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-192">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="5de4b-193">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-193">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="5de4b-194">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-194">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="5de4b-195">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-195">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5de4b-196">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5de4b-196">Additional resources</span></span>

* [<span data-ttu-id="5de4b-197">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-197">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="5de4b-198">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="5de4b-198">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="5de4b-199">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="5de4b-199">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="5de4b-200">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-200">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="5de4b-201">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-201">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="5de4b-202">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-202">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="5de4b-203">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-203">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="5de4b-204">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-204">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="5de4b-205">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-205">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="5de4b-206">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-206">Doesn't use local memory.</span></span>

<span data-ttu-id="5de4b-207">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-207">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="5de4b-208">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-208">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="5de4b-209">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-209">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="5de4b-210">선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-210">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="5de4b-211">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5de4b-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5de4b-212">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-212">Prerequisites</span></span>

<span data-ttu-id="5de4b-213">SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="5de4b-213">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="5de4b-214">Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-214">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="5de4b-215">Redis 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-215">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="5de4b-216">NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다..</span><span class="sxs-lookup"><span data-stu-id="5de4b-216">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="5de4b-217">NCache 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 NCache 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-217">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="5de4b-218">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="5de4b-218">IDistributedCache interface</span></span>

<span data-ttu-id="5de4b-219"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-219">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="5de4b-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="5de4b-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="5de4b-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="5de4b-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="5de4b-224">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="5de4b-224">Establish distributed caching services</span></span>

<span data-ttu-id="5de4b-225">에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-225">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5de4b-226">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-226">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="5de4b-227">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-227">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="5de4b-228">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-228">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="5de4b-229">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-229">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="5de4b-230">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-230">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="5de4b-231">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-231">Distributed Memory Cache</span></span>

<span data-ttu-id="5de4b-232">분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-232">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="5de4b-233">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-233">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="5de4b-234">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-234">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="5de4b-235">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-235">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="5de4b-236">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="5de4b-236">In development and testing scenarios.</span></span>
* <span data-ttu-id="5de4b-237">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-237">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="5de4b-238">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-238">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="5de4b-239">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-239">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="5de4b-240">샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-240">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="5de4b-241">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-241">Distributed SQL Server Cache</span></span>

<span data-ttu-id="5de4b-242">분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-242">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="5de4b-243">SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-243">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="5de4b-244">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-244">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="5de4b-245">명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-245">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="5de4b-246">SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-246">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="5de4b-247">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-247">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="5de4b-248">도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-248">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="5de4b-250">앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-250">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="5de4b-251">샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-251">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="5de4b-252"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 appsettings에 의해 저장 됨) *appsettings.json* / *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="5de4b-252">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="5de4b-253">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-253">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="5de4b-254">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-254">Distributed Redis Cache</span></span>

<span data-ttu-id="5de4b-255">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-255">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="5de4b-256">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-256">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="5de4b-257">앱은 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 다음과 같은 개발 이외의 환경에서 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-257">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="5de4b-258">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-258">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="5de4b-259">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-259">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="5de4b-260">`redis-server`명령 프롬프트에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-260">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="5de4b-261">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-261">Distributed NCache Cache</span></span>

<span data-ttu-id="5de4b-262">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-262">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="5de4b-263">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-263">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="5de4b-264">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [Windows 용 시작 가이드 (.net 및 .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-264">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="5de4b-265">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="5de4b-265">To configure NCache:</span></span>

1. <span data-ttu-id="5de4b-266">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-266">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="5de4b-267">[Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-267">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="5de4b-268">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-268">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="5de4b-269">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="5de4b-269">Use the distributed cache</span></span>

<span data-ttu-id="5de4b-270">인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-270">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="5de4b-271">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-271">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5de4b-272">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-272">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="5de4b-273">현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-273">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="5de4b-274">샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-274">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="5de4b-275">인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-275">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="5de4b-276">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-276">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="5de4b-277">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료* 가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-277">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="5de4b-278">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-278">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="5de4b-279">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-279">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="5de4b-280">인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="5de4b-280">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="5de4b-281"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-281">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="5de4b-282">권장 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-282">Recommendations</span></span>

<span data-ttu-id="5de4b-283"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-283">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="5de4b-284">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="5de4b-284">Existing infrastructure</span></span>
* <span data-ttu-id="5de4b-285">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-285">Performance requirements</span></span>
* <span data-ttu-id="5de4b-286">비용</span><span class="sxs-lookup"><span data-stu-id="5de4b-286">Cost</span></span>
* <span data-ttu-id="5de4b-287">팀 환경</span><span class="sxs-lookup"><span data-stu-id="5de4b-287">Team experience</span></span>

<span data-ttu-id="5de4b-288">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-288">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="5de4b-289">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-289">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="5de4b-290">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-290">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="5de4b-291">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-291">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="5de4b-292">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-292">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="5de4b-293">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-293">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5de4b-294">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5de4b-294">Additional resources</span></span>

* [<span data-ttu-id="5de4b-295">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-295">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="5de4b-296">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="5de4b-296">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="5de4b-297">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="5de4b-297">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="5de4b-298">분산 캐시는 여러 앱 서버에서 공유 하는 캐시로, 일반적으로 액세스 하는 앱 서버에 외부 서비스로 유지 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-298">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="5de4b-299">분산 캐시는 특히 앱이 클라우드 서비스 또는 서버 팜에서 호스트 되는 경우 ASP.NET Core 앱의 성능과 확장성을 향상 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-299">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="5de4b-300">분산 캐시는 개별 앱 서버에 캐시 된 데이터가 저장 되는 다른 캐싱 시나리오에 비해 여러 가지 이점을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-300">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="5de4b-301">캐시 된 데이터를 배포 하는 경우 데이터는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-301">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="5de4b-302">는 여러 서버에 대 한 여러 요청 *에서 일관 되* 고 일관 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-302">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="5de4b-303">서버를 다시 시작 하 고 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-303">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="5de4b-304">로컬 메모리를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-304">Doesn't use local memory.</span></span>

<span data-ttu-id="5de4b-305">분산 캐시 구성은 구현 별로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-305">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="5de4b-306">이 문서에서는 SQL Server 및 Redis 분산 캐시를 구성 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-306">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="5de4b-307">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-307">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="5de4b-308">선택한 구현과 관계 없이 앱은 인터페이스를 사용 하 여 캐시와 상호 작용 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-308">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="5de4b-309">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5de4b-309">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5de4b-310">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-310">Prerequisites</span></span>

<span data-ttu-id="5de4b-311">SQL Server 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하거나 패키지 참조를 [패키지에 추가 합니다.](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="5de4b-311">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="5de4b-312">Redis 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 패키지에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-312">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="5de4b-313">Redis 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 Redis 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-313">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="5de4b-314">NCache 분산 캐시를 사용 하려면 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 를 참조 하 고 패키지 참조를 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 에 추가 합니다..</span><span class="sxs-lookup"><span data-stu-id="5de4b-314">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="5de4b-315">NCache 패키지는 패키지에 포함 되지 `Microsoft.AspNetCore.App` 않으므로 프로젝트 파일에서 개별적으로 NCache 패키지를 참조 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-315">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="5de4b-316">IDistributedCache 인터페이스</span><span class="sxs-lookup"><span data-stu-id="5de4b-316">IDistributedCache interface</span></span>

<span data-ttu-id="5de4b-317"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>인터페이스는 분산 캐시 구현에서 항목을 조작 하는 다음과 같은 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-317">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="5de4b-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : 문자열 키를 허용 하 고 `byte[]` 캐시 된 항목을 캐시에 있는 경우 배열로 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="5de4b-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : `byte[]` 문자열 키를 사용 하 여 캐시에 항목 (배열)을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="5de4b-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : 해당 키에 따라 캐시에서 항목을 새로 고쳐 슬라이딩 만료 시간 제한 (있는 경우)을 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="5de4b-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : 문자열 키를 기준으로 캐시 항목을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="5de4b-322">분산 캐싱 서비스 설정</span><span class="sxs-lookup"><span data-stu-id="5de4b-322">Establish distributed caching services</span></span>

<span data-ttu-id="5de4b-323">에서의 구현을 등록 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices` 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-323">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5de4b-324">이 항목에서 설명 하는 프레임 워크 제공 구현에는 다음이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-324">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="5de4b-325">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-325">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="5de4b-326">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-326">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="5de4b-327">Distributed Redis cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-327">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="5de4b-328">Distributed NCache cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-328">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="5de4b-329">분산 메모리 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-329">Distributed Memory Cache</span></span>

<span data-ttu-id="5de4b-330">분산 메모리 캐시 ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> )는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 메모리에 항목을 저장 하는 프레임 워크에서 제공 하는 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-330">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="5de4b-331">분산 메모리 캐시는 실제 분산 된 캐시가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-331">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="5de4b-332">캐시 된 항목은 앱이 실행 되 고 있는 서버의 앱 인스턴스에 의해 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-332">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="5de4b-333">분산 메모리 캐시는 다음과 같은 유용한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-333">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="5de4b-334">개발 및 테스트 시나리오에서</span><span class="sxs-lookup"><span data-stu-id="5de4b-334">In development and testing scenarios.</span></span>
* <span data-ttu-id="5de4b-335">프로덕션 환경에서 단일 서버를 사용 하는 경우 메모리 소비가 문제가 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-335">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="5de4b-336">분산 메모리 캐시를 구현 하면 캐시 된 데이터 저장소를 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-336">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="5de4b-337">이를 통해 여러 노드 또는 내결함성이 필요한 경우 나중에 진정한 분산 캐싱 솔루션을 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-337">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="5de4b-338">샘플 앱은 앱이의 개발 환경에서 실행 될 때 분산 메모리 캐시를 사용 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-338">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="5de4b-339">분산 SQL Server 캐시</span><span class="sxs-lookup"><span data-stu-id="5de4b-339">Distributed SQL Server Cache</span></span>

<span data-ttu-id="5de4b-340">분산 SQL Server 캐시 구현 ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> )을 사용 하면 분산 캐시에서 SQL Server 데이터베이스를 백업 저장소로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-340">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="5de4b-341">SQL Server 인스턴스에서 캐시 된 SQL Server 항목 테이블을 만들려면 도구를 사용할 수 있습니다 `sql-cache` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-341">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="5de4b-342">이 도구는 사용자가 지정한 이름 및 스키마를 사용 하 여 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-342">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="5de4b-343">명령을 실행 하 여 SQL Server에서 테이블을 만듭니다 `sql-cache create` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-343">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="5de4b-344">SQL Server 인스턴스 ( `Data Source` ), 데이터베이스 ( `Initial Catalog` ), 스키마 (예 `dbo` :) 및 테이블 이름 (예 `TestCache` :)을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-344">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="5de4b-345">도구가 성공 했음을 나타내는 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-345">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="5de4b-346">도구에서 만든 테이블에는 `sql-cache` 다음 스키마가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-346">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 캐시 테이블](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="5de4b-348">앱은가 아닌 인스턴스를 사용 하 여 캐시 값을 조작 해야 합니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-348">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="5de4b-349">샘플 앱은 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 다음과 같은 개발 이외의 환경에서을 구현 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-349">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="5de4b-350"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(및 필요에 따라 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 및 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> )는 일반적으로 소스 제어 외부에 저장 됩니다 (예: [암호 관리자](xref:security/app-secrets) 또는 appsettings에 의해 저장 됨) *appsettings.json* / *. 환경}. json* 파일).</span><span class="sxs-lookup"><span data-stu-id="5de4b-350">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="5de4b-351">연결 문자열에는 원본 제어 시스템에서 유지 되어야 하는 자격 증명이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-351">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="5de4b-352">분산 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-352">Distributed Redis Cache</span></span>

<span data-ttu-id="5de4b-353">[Redis](https://redis.io/) 는 분산 캐시로 자주 사용 되는 오픈 소스 메모리 내 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-353">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="5de4b-354">Redis를 로컬로 사용할 수 있으며, Azure에서 호스트 되는 ASP.NET Core 앱에 대 한 [Azure Redis Cache](https://azure.microsoft.com/services/cache/) 를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-354">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="5de4b-355">앱은 인스턴스 ()를 사용 하 여 캐시 구현을 구성 합니다 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> .</span><span class="sxs-lookup"><span data-stu-id="5de4b-355">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="5de4b-356">로컬 컴퓨터에 Redis를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-356">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="5de4b-357">[Chocolatey Redis 패키지](https://chocolatey.org/packages/redis-64/)를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-357">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="5de4b-358">`redis-server`명령 프롬프트에서를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-358">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="5de4b-359">Distributed NCache Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-359">Distributed NCache Cache</span></span>

<span data-ttu-id="5de4b-360">[NCache](https://github.com/Alachisoft/NCache) 는 .NET 및 .net Core에서 기본적으로 개발 된 오픈 소스 메모리 내 분산 캐시입니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-360">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="5de4b-361">NCache는 로컬에서 작동 하 고 Azure 또는 다른 호스팅 플랫폼에서 실행 되는 ASP.NET Core 앱에 대 한 분산 캐시 클러스터로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-361">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="5de4b-362">로컬 컴퓨터에 NCache를 설치 및 구성 하려면 [Windows 용 시작 가이드 (.net 및 .Net Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-362">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="5de4b-363">NCache를 구성 하려면:</span><span class="sxs-lookup"><span data-stu-id="5de4b-363">To configure NCache:</span></span>

1. <span data-ttu-id="5de4b-364">[NCache 오픈 소스 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-364">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="5de4b-365">[Ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)에서 캐시 클러스터를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-365">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="5de4b-366">다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-366">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="5de4b-367">분산 캐시 사용</span><span class="sxs-lookup"><span data-stu-id="5de4b-367">Use the distributed cache</span></span>

<span data-ttu-id="5de4b-368">인터페이스를 사용 하려면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 앱의 모든 생성자에서의 인스턴스를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-368">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="5de4b-369">인스턴스는 [DI (종속성 주입)](xref:fundamentals/dependency-injection)에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-369">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5de4b-370">샘플 앱이 시작 되 면 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 가에 삽입 됩니다 `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-370">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="5de4b-371">현재 시간은를 사용 하 여 캐시 됩니다. <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 자세한 내용은 [웹 호스트: IApplicationLifetime 인터페이스](xref:fundamentals/host/web-host#iapplicationlifetime-interface)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="5de4b-371">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="5de4b-372">샘플 앱은 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel` 인덱스 페이지에서 사용 하기 위해를에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-372">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="5de4b-373">인덱스 페이지가 로드 될 때마다에서 캐시 된 시간에 대 한 캐시를 확인 합니다 `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="5de4b-373">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="5de4b-374">캐시 된 시간이 만료 되지 않은 경우에는 시간이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-374">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="5de4b-375">캐시 된 시간에 마지막으로 액세스 한 후 20 초가 경과한 경우 (이 페이지가 마지막으로 로드 된 시간) 페이지에는 *캐시 된 시간 만료* 가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-375">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="5de4b-376">캐시 된 시간 **다시 설정** 단추를 선택 하 여 캐시 된 시간을 현재 시간으로 즉시 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-376">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="5de4b-377">단추는 `OnPostResetCachedTime` 처리기 메서드를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-377">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="5de4b-378">인스턴스에 대해 Singleton 또는 범위 수명을 사용할 필요는 없습니다 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (최소한 기본 제공 구현의 경우).</span><span class="sxs-lookup"><span data-stu-id="5de4b-378">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="5de4b-379"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>DI를 사용 하는 대신 인스턴스를 만들 수도 있지만, 코드에서 인스턴스를 만들면 코드를 테스트 하는 것이 어렵고 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 위반 하 게 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-379">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="5de4b-380">권장 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-380">Recommendations</span></span>

<span data-ttu-id="5de4b-381"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>앱에 가장 적합 한 구현을 결정할 때 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-381">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="5de4b-382">기존 인프라</span><span class="sxs-lookup"><span data-stu-id="5de4b-382">Existing infrastructure</span></span>
* <span data-ttu-id="5de4b-383">성능 요구 사항</span><span class="sxs-lookup"><span data-stu-id="5de4b-383">Performance requirements</span></span>
* <span data-ttu-id="5de4b-384">비용</span><span class="sxs-lookup"><span data-stu-id="5de4b-384">Cost</span></span>
* <span data-ttu-id="5de4b-385">팀 환경</span><span class="sxs-lookup"><span data-stu-id="5de4b-385">Team experience</span></span>

<span data-ttu-id="5de4b-386">캐싱 솔루션은 일반적으로 메모리 내 저장소를 사용 하 여 캐시 된 데이터를 신속 하 게 검색 하지만 메모리는 제한 된 리소스 이며 확장 하는 데 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-386">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="5de4b-387">일반적으로 사용 되는 데이터를 캐시에만 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-387">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="5de4b-388">일반적으로 Redis cache는 SQL Server 캐시 보다 높은 처리량과 짧은 대기 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-388">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="5de4b-389">그러나 일반적으로 벤치마킹은 캐싱 전략의 성능 특성을 결정 하는 데 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-389">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="5de4b-390">SQL Server를 분산 캐시 백업 저장소로 사용 하는 경우 캐시에 대해 동일한 데이터베이스를 사용 하 고 앱의 일반 데이터 저장 및 검색을 사용 하면 두 성능에 부정적인 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-390">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="5de4b-391">분산 캐시 백업 저장소에 전용 SQL Server 인스턴스를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="5de4b-391">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5de4b-392">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5de4b-392">Additional resources</span></span>

* [<span data-ttu-id="5de4b-393">Azure의 Redis Cache</span><span class="sxs-lookup"><span data-stu-id="5de4b-393">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="5de4b-394">Azure의 SQL Database</span><span class="sxs-lookup"><span data-stu-id="5de4b-394">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="5de4b-395">[웹 팜의 ASP.NET Core IDistributedCache Provider For NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub의 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="5de4b-395">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
