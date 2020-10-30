---
title: ASP.NET Core의 분산 캐시 태그 도우미
author: pkellner
description: 분산 캐시 태그 도우미를 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
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
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 04ab5be4d9cec066a4b7cd422a1566bcbb5a291a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061160"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>ASP.NET Core의 분산 캐시 태그 도우미

작성자: [Peter Kellner](https://peterkellner.net)

분산 캐시 태그 도우미는 ASP.NET Core 앱의 콘텐츠를 분산 캐시 원본에 캐싱하여 성능을 획기적으로 개선하는 기능을 제공합니다.

태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 기본 클래스를 상속받습니다. 모든 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) 특성을 분산 태그 도우미에서 사용할 수 있습니다.

분산 캐시 태그 도우미는 [생성자 주입](xref:fundamentals/dependency-injection#constructor-injection-behavior)을 사용합니다. 분산 캐시 태그 도우미의 생성자에는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스가 전달됩니다. `Startup.ConfigureServices`( *Startup.cs* )에서 `IDistributedCache`의 구체적 구현이 생성되지 않으면, 분산 캐시 태그 도우미는 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)와 동일한 메모리 내 공급자를 사용하여 캐시된 데이터를 저장합니다.

## <a name="distributed-cache-tag-helper-attributes"></a>분산 캐시 태그 도우미 특성

### <a name="attributes-shared-with-the-cache-tag-helper"></a>캐시 태그 도우미와 공유되는 특성

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 클래스를 상속받습니다. 이러한 특성에 대한 설명은 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)를 참조하세요.

### <a name="name"></a>name

| 특성 유형 | 예제                               |
| -------------- | ------------------------------------- |
| String         | `my-distributed-cache-unique-key-101` |

`name`은 필수입니다. `name` 특성은 저장된 각 캐시 인스턴스의 키로 사용됩니다. 페이지의 페이지 이름과 위치를 기준으로 각 인스턴스에 캐시 키를 할당 하는 캐시 태그 도우미와 달리 Razor Razor 분산 캐시 태그 도우미는 특성에 대 한 키만을 기준으로 `name` 합니다.

예제:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>분산 캐시 태그 도우미 IDistributedCache 구현

ASP.NET Core에는 두 가지 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현이 기본으로 제공됩니다. 하나는 SQL Server 기반이고 다른 하나는 Redis 기반입니다. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)와 같은 타사 구현도 사용할 수 있습니다. 이러한 구현의 세부 정보는 <xref:performance/caching/distributed>에서 찾을 수 있습니다. 두 가지 구현 모두 `Startup`에서 `IDistributedCache` 인스턴스를 설정해야 합니다.

특정 `IDistributedCache` 구현의 사용과 특별히 관련된 태그 특성은 없습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
