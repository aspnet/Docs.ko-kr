---
title: ASP.NET Core에서 해지 된 키를 포함 하는 페이로드 보호 해제
author: rick-anderson
description: ASP.NET Core 앱에서 해지 된 후의 키로 보호 되는 데이터의 보호를 해제 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051098"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="7db95-103">ASP.NET Core에서 해지 된 키를 포함 하는 페이로드 보호 해제</span><span class="sxs-lookup"><span data-stu-id="7db95-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="7db95-104">ASP.NET Core 데이터 보호 Api는 주로 기밀 페이로드의 무한 지 속성에 적합 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="7db95-105">[WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi) 및 [Azure Rights Management](/rights-management/) 와 같은 기타 기술은 무한 저장소의 시나리오에 보다 적합 하며 강력한 키 관리 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="7db95-106">즉, 기밀 데이터의 장기 보호를 위해 개발자가 ASP.NET Core 데이터 보호 Api를 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="7db95-107">키가 키 링에서 제거 되지 않으므로 키가 `IDataProtector.Unprotect` 사용 가능 하 고 유효한 경우 항상 기존 페이로드를 복구할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="7db95-108">그러나 개발자가 해지 된 키로 보호 되는 데이터를 보호 해제 하려고 할 때 문제가 발생 `IDataProtector.Unprotect` 합니다 .이 경우에서 예외가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="7db95-109">이러한 종류의 페이로드를 시스템에서 쉽게 다시 만들 수 있으며, 최악의 경우 사이트 방문자가 다시 로그인 해야 할 수 있으므로 수명이 짧거나 임시 페이로드 (예: 인증 토큰)에는 문제가 없을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="7db95-110">그러나 지속형 페이로드의 경우 throw로 `Unprotect` 인해 데이터 손실이 허용 되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="7db95-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="7db95-111">IPersistedDataProtector</span></span>

<span data-ttu-id="7db95-112">해지 된 키가 있는 경우에도 페이로드의 보호를 해제할 수 있도록 하는 시나리오를 지원 하기 위해 데이터 보호 시스템에는 형식이 포함 되어 있습니다 `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="7db95-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="7db95-113">인스턴스를 가져오려면 `IPersistedDataProtector` 단순히의 인스턴스를 `IDataProtector` 일반적인 방식으로 가져와로 캐스팅 해 봅니다 `IDataProtector` `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="7db95-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="7db95-114">모든 `IDataProtector` 인스턴스를로 캐스팅할 수 있는 것은 아닙니다 `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="7db95-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="7db95-115">개발자는 c # As 연산자를 사용 하 여 잘못 된 캐스트로 인해 발생 하는 런타임 예외를 방지 해야 하며, 실패 사례를 적절 하 게 처리할 수 있도록 준비 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="7db95-116">`IPersistedDataProtector` 는 다음 API 표면을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="7db95-117">이 API는 보호 된 페이로드 (바이트 배열)를 사용 하 고 보호 되지 않는 페이로드를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="7db95-118">문자열 기반 오버 로드가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-118">There's no string-based overload.</span></span> <span data-ttu-id="7db95-119">두 out 매개 변수는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="7db95-120">`requiresMigration`:이 페이로드를 보호 하는 데 사용 된 키가 더 이상 활성 기본 키가 아닌 경우 true로 설정 됩니다. 예를 들어,이 페이로드를 보호 하는 데 사용 되는 키가 오래 되어 키 롤링 작업이 수행 된 후에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="7db95-121">호출자는 비즈니스 요구에 따라 페이로드를 다시 보호 하는 것을 고려할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="7db95-122">`wasRevoked`:이 페이로드를 보호 하는 데 사용 된 키가 해지 된 경우 true로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="7db95-123">메서드에 전달할 때는 매우 주의 해야 `ignoreRevocationErrors: true` `DangerousUnprotect` 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="7db95-124">이 메서드를 호출한 후에는 `wasRevoked` 값이 true 이면이 페이로드를 보호 하는 데 사용 된 키가 해지 되 고 페이로드의 신뢰성은 주의 대상으로 처리 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="7db95-125">이 경우 신뢰할 수 없는 웹 클라이언트에서 전송 하는 것이 아니라 보안 데이터베이스에서 제공 되는 것과 같이 신뢰할 수 있는 것으로 확신 하는 경우에만 보호 되지 않는 페이로드에서 계속 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="7db95-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
