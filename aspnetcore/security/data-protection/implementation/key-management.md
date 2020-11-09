---
title: ASP.NET Core의 키 관리
author: rick-anderson
description: ASP.NET Core Data Protection key management Api에 대 한 구현 세부 정보를 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: f7e0fa5e80208469188a11239a9453ecafdcf5f3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060198"
---
# <a name="key-management-in-aspnet-core"></a><span data-ttu-id="7339f-103">ASP.NET Core의 키 관리</span><span class="sxs-lookup"><span data-stu-id="7339f-103">Key management in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-management"></a>

<span data-ttu-id="7339f-104">데이터 보호 시스템은 페이로드를 보호 하 고 보호를 해제 하는 데 사용 되는 마스터 키의 수명을 자동으로 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-104">The data protection system automatically manages the lifetime of master keys used to protect and unprotect payloads.</span></span> <span data-ttu-id="7339f-105">각 키는 다음 네 단계 중 하나에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-105">Each key can exist in one of four stages:</span></span>

* <span data-ttu-id="7339f-106">Created-키가 키 링에 있지만 아직 활성화 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-106">Created - the key exists in the key ring but has not yet been activated.</span></span> <span data-ttu-id="7339f-107">키가이 키 링을 사용 하는 모든 컴퓨터에 전파 될 수 있는 충분 한 시간이 경과할 때까지 새 보호 작업에 키를 사용 하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-107">The key shouldn't be used for new Protect operations until sufficient time has elapsed that the key has had a chance to propagate to all machines that are consuming this key ring.</span></span>

* <span data-ttu-id="7339f-108">활성-키 링에 키가 있으며 모든 새 보호 작업에 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-108">Active - the key exists in the key ring and should be used for all new Protect operations.</span></span>

* <span data-ttu-id="7339f-109">만료 됨-키가 기본 수명을 실행 하 고 새 보호 작업에 더 이상 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-109">Expired - the key has run its natural lifetime and should no longer be used for new Protect operations.</span></span>

* <span data-ttu-id="7339f-110">해지 됨-키가 손상 되어 새 보호 작업에 사용 되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-110">Revoked - the key is compromised and must not be used for new Protect operations.</span></span>

<span data-ttu-id="7339f-111">생성, 활성 및 만료 된 키는 모두 들어오는 페이로드의 보호를 해제 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-111">Created, active, and expired keys may all be used to unprotect incoming payloads.</span></span> <span data-ttu-id="7339f-112">기본적으로 해지 된 키를 사용 하 여 페이로드를 보호 하지 않을 수 있지만, 필요한 경우 응용 프로그램 개발자가 [이 동작을 재정의할](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-112">Revoked keys by default may not be used to unprotect payloads, but the application developer can [override this behavior](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) if necessary.</span></span>

>[!WARNING]
> <span data-ttu-id="7339f-113">개발자는 키 링 (예: 파일 시스템에서 해당 파일 삭제)에서 키를 삭제 하려고 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-113">The developer might be tempted to delete a key from the key ring (e.g., by deleting the corresponding file from the file system).</span></span> <span data-ttu-id="7339f-114">이 시점에서 키로 보호 되는 모든 데이터는 영구적으로 undecipherable 해지 된 키가 있는 것 처럼 비상 재정의가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-114">At that point, all data protected by the key is permanently undecipherable, and there's no emergency override like there's with revoked keys.</span></span> <span data-ttu-id="7339f-115">키를 삭제 하는 것은 진정한 파괴적인 동작 이므로 데이터 보호 시스템은이 작업을 수행 하기 위한 최고 수준의 API를 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-115">Deleting a key is truly destructive behavior, and consequently the data protection system exposes no first-class API for performing this operation.</span></span>

## <a name="default-key-selection"></a><span data-ttu-id="7339f-116">기본 키 선택</span><span class="sxs-lookup"><span data-stu-id="7339f-116">Default key selection</span></span>

<span data-ttu-id="7339f-117">데이터 보호 시스템은 백업 리포지토리에서 키 링을 읽을 때 키 링에서 "기본" 키를 찾으려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-117">When the data protection system reads the key ring from the backing repository, it will attempt to locate a "default" key from the key ring.</span></span> <span data-ttu-id="7339f-118">기본 키는 새 보호 작업에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-118">The default key is used for new Protect operations.</span></span>

<span data-ttu-id="7339f-119">일반적인 추론은 데이터 보호 시스템이 가장 최근의 활성화 날짜를 기본 키로 사용 하 여 키를 선택 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-119">The general heuristic is that the data protection system chooses the key with the most recent activation date as the default key.</span></span> <span data-ttu-id="7339f-120">서버 간 클럭 오차를 허용 하는 작은 fudge 요인이 있습니다. 키가 만료 되거나 해지 된 경우 및 응용 프로그램에서 자동 키 생성을 사용 하지 않도록 설정 하지 않은 경우 아래의 [키 만료 및 롤링](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) 정책에 따라 즉시 활성화 하 여 새 키가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-120">(There's a small fudge factor to allow for server-to-server clock skew.) If the key is expired or revoked, and if the application has not disabled automatic key generation, then a new key will be generated with immediate activation per the [key expiration and rolling](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) policy below.</span></span>

<span data-ttu-id="7339f-121">데이터 보호 시스템에서 다른 키로 대체 하는 대신 새 키를 즉시 생성 하는 이유는 새 키 생성이 새 키 이전에 활성화 된 모든 키의 암시적 만료로 처리 되어야 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-121">The reason the data protection system generates a new key immediately rather than falling back to a different key is that new key generation should be treated as an implicit expiration of all keys that were activated prior to the new key.</span></span> <span data-ttu-id="7339f-122">일반적인 개념은 새 키가 이전 키 보다 다른 알고리즘 또는 미사용 암호화 메커니즘을 사용 하 여 구성 된 것일 수 있으며 시스템은 현재 구성을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-122">The general idea is that new keys may have been configured with different algorithms or encryption-at-rest mechanisms than old keys, and the system should prefer the current configuration over falling back.</span></span>

<span data-ttu-id="7339f-123">예외가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-123">There's an exception.</span></span> <span data-ttu-id="7339f-124">응용 프로그램 개발자가 [자동 키 생성을 사용 하지 않도록](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)설정한 경우 데이터 보호 시스템은 기본 키로 항목을 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-124">If the application developer has [disabled automatic key generation](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), then the data protection system must choose something as the default key.</span></span> <span data-ttu-id="7339f-125">이 대체 시나리오에서 시스템은 가장 최근의 활성화 날짜를 사용 하 여 해지 되지 않은 키를 선택 하 고, 클러스터의 다른 컴퓨터로 전파 하는 데 시간이 오래 걸리는 키에 대 한 기본 설정을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-125">In this fallback scenario, the system will choose the non-revoked key with the most recent activation date, with preference given to keys that have had time to propagate to other machines in the cluster.</span></span> <span data-ttu-id="7339f-126">대체 (fallback) 시스템에서 만료 된 기본 키를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-126">The fallback system may end up choosing an expired default key as a result.</span></span> <span data-ttu-id="7339f-127">대체 (fallback) 시스템은 해지 된 키를 기본 키로 선택 하지 않으며, 키 링이 비어 있거나 모든 키가 해지 된 경우 초기화 시 시스템에서 오류를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-127">The fallback system will never choose a revoked key as the default key, and if the key ring is empty or every key has been revoked then the system will produce an error upon initialization.</span></span>

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a><span data-ttu-id="7339f-128">키 만료 및 롤링</span><span class="sxs-lookup"><span data-stu-id="7339f-128">Key expiration and rolling</span></span>

<span data-ttu-id="7339f-129">키를 만들 때 자동으로 활성화 날짜를 {now + 2 days}로 지정 하 고 만료 날짜를 {now + 90 days}로 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-129">When a key is created, it's automatically given an activation date of { now + 2 days } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="7339f-130">활성화 하기 전 2 일 지연 시간은 시스템을 통해 전파 되는 키 시간을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-130">The 2-day delay before activation gives the key time to propagate through the system.</span></span> <span data-ttu-id="7339f-131">즉, 백업 저장소를 가리키는 다른 응용 프로그램이 다음 자동 새로 고침 기간에 키를 관찰할 수 있으므로 키 링이 활성화 될 때이를 사용 해야 할 수 있는 모든 응용 프로그램에 전파 될 가능성을 극대화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-131">That is, it allows other applications pointing at the backing store to observe the key at their next auto-refresh period, thus maximizing the chances that when the key ring does become active it has propagated to all applications that might need to use it.</span></span>

<span data-ttu-id="7339f-132">기본 키가 2 일 이내에 만료 되 고 키 링에 기본 키가 만료 될 때 활성화 되는 키가 아직 없는 경우 데이터 보호 시스템은 자동으로 키 링에 새 키를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-132">If the default key will expire within 2 days and if the key ring doesn't already have a key that will be active upon expiration of the default key, then the data protection system will automatically persist a new key to the key ring.</span></span> <span data-ttu-id="7339f-133">이 새 키에는 {기본 키의 만료 날짜}의 활성화 날짜와 {now + 90 days}의 만료 날짜가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-133">This new key has an activation date of { default key's expiration date } and an expiration date of { now + 90 days }.</span></span> <span data-ttu-id="7339f-134">이렇게 하면 시스템에서 서비스 중단 없이 정기적으로 키를 자동으로 롤백할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-134">This allows the system to automatically roll keys on a regular basis with no interruption of service.</span></span>

<span data-ttu-id="7339f-135">즉시 활성화를 사용 하 여 키가 생성 되는 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-135">There might be circumstances where a key will be created with immediate activation.</span></span> <span data-ttu-id="7339f-136">한 가지 예는 응용 프로그램이 한 번에 실행 되지 않았고 키 링의 모든 키가 만료 된 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-136">One example would be when the application hasn't run for a time and all keys in the key ring are expired.</span></span> <span data-ttu-id="7339f-137">이 경우 정상 2 일 활성화 지연 없이 키에 {now}의 활성화 날짜가 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-137">When this happens, the key is given an activation date of { now } without the normal 2-day activation delay.</span></span>

<span data-ttu-id="7339f-138">기본 키 수명은 90 일 이지만 다음 예제와 같이 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-138">The default key lifetime is 90 days, though this is configurable as in the following example.</span></span>

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

<span data-ttu-id="7339f-139">에 대 한 명시적 호출이 `SetDefaultKeyLifetime` 시스템 차원의 정책을 재정의 하더라도 관리자는 기본 시스템 전체를 변경할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-139">An administrator can also change the default system-wide, though an explicit call to `SetDefaultKeyLifetime` will override any system-wide policy.</span></span> <span data-ttu-id="7339f-140">기본 키 수명은 7 일 보다 짧을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-140">The default key lifetime cannot be shorter than 7 days.</span></span>

## <a name="automatic-key-ring-refresh"></a><span data-ttu-id="7339f-141">자동 키 링 새로 고침</span><span class="sxs-lookup"><span data-stu-id="7339f-141">Automatic key ring refresh</span></span>

<span data-ttu-id="7339f-142">데이터 보호 시스템이 초기화 되 면 기본 리포지토리에서 키 링을 읽고 메모리에 캐시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-142">When the data protection system initializes, it reads the key ring from the underlying repository and caches it in memory.</span></span> <span data-ttu-id="7339f-143">이 캐시를 사용 하면 보호 및 보호 해제 작업이 백업 저장소에 도달 하지 않고 계속 진행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-143">This cache allows Protect and Unprotect operations to proceed without hitting the backing store.</span></span> <span data-ttu-id="7339f-144">시스템은 백업 저장소에서 약 24 시간 마다 또는 현재 기본 키가 만료 되는 시간 중 먼저 발생 하는 시간에 대 한 변경 내용을 자동으로 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-144">The system will automatically check the backing store for changes approximately every 24 hours or when the current default key expires, whichever comes first.</span></span>

>[!WARNING]
> <span data-ttu-id="7339f-145">개발자는 키 관리 Api를 직접 사용 해야 하는 경우는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-145">Developers should very rarely (if ever) need to use the key management APIs directly.</span></span> <span data-ttu-id="7339f-146">데이터 보호 시스템은 위에서 설명한 대로 자동 키 관리를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-146">The data protection system will perform automatic key management as described above.</span></span>

<span data-ttu-id="7339f-147">데이터 보호 시스템은 `IKeyManager` 키 링을 검사 하 고 변경 하는 데 사용할 수 있는 인터페이스를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-147">The data protection system exposes an interface `IKeyManager` that can be used to inspect and make changes to the key ring.</span></span> <span data-ttu-id="7339f-148">인스턴스를 제공한 DI 시스템은 사용 `IDataProtectionProvider` 에 대 한 인스턴스를 제공할 수도 있습니다 `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="7339f-148">The DI system that provided the instance of `IDataProtectionProvider` can also provide an instance of `IKeyManager` for your consumption.</span></span> <span data-ttu-id="7339f-149">또는 `IKeyManager` `IServiceProvider` 아래 예제와 같이에서 직접를 끌어올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-149">Alternatively, you can pull the `IKeyManager` straight from the `IServiceProvider` as in the example below.</span></span>

<span data-ttu-id="7339f-150">키 링을 수정 하는 모든 작업 (명시적으로 또는 해지를 수행 하는 새 키를 만드는 작업)은 메모리 내 캐시를 무효화 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-150">Any operation which modifies the key ring (creating a new key explicitly or performing a revocation) will invalidate the in-memory cache.</span></span> <span data-ttu-id="7339f-151">또는에 대 한 다음 호출로 `Protect` `Unprotect` 인해 데이터 보호 시스템이 키 링을 다시 읽고 캐시를 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-151">The next call to `Protect` or `Unprotect` will cause the data protection system to reread the key ring and recreate the cache.</span></span>

<span data-ttu-id="7339f-152">아래 샘플에서는 기존 키를 취소 하 `IKeyManager` 고 수동으로 새 키를 생성 하는 등의 방법으로 인터페이스를 사용 하 여 키 링을 검사 및 조작 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-152">The sample below demonstrates using the `IKeyManager` interface to inspect and manipulate the key ring, including revoking existing keys and generating a new key manually.</span></span>

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a><span data-ttu-id="7339f-153">키 스토리지</span><span class="sxs-lookup"><span data-stu-id="7339f-153">Key storage</span></span>

<span data-ttu-id="7339f-154">데이터 보호 시스템에는 적절 한 키 저장소 위치 및 암호화 미사용 메커니즘이 자동으로 추론 되도록 하는 추론 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-154">The data protection system has a heuristic whereby it attempts to deduce an appropriate key storage location and encryption-at-rest mechanism automatically.</span></span> <span data-ttu-id="7339f-155">키 지 속성 메커니즘은 앱 개발자도 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-155">The key persistence mechanism is also configurable by the app developer.</span></span> <span data-ttu-id="7339f-156">다음 문서에서는 이러한 메커니즘의 기본 구현에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="7339f-156">The following documents discuss the in-box implementations of these mechanisms:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
