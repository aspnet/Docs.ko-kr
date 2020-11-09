---
title: ASP.NET Core의 용도 문자열
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api에서 용도 문자열이 사용 되는 방법에 대해 알아봅니다.
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060809"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="be677-103">ASP.NET Core의 용도 문자열</span><span class="sxs-lookup"><span data-stu-id="be677-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="be677-104">을 사용 하는 구성 요소는 `IDataProtectionProvider` 메서드에 고유한 *용도* 매개 변수를 전달 해야 합니다 `CreateProtector` .</span><span class="sxs-lookup"><span data-stu-id="be677-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="be677-105">목적 *매개 변수* 는 루트 암호화 키가 동일한 경우에도 암호화 소비자 간의 격리를 제공 하므로 데이터 보호 시스템의 보안에 내재 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="be677-106">소비자가 용도를 지정 하는 경우 용도 문자열이 루트 암호화 키와 함께 사용 되어 해당 소비자에 고유한 암호화 하위 키를 파생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="be677-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="be677-107">이렇게 하면 다른 구성 요소에서 해당 페이로드를 읽을 수 없으며 다른 구성 요소의 페이로드를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="be677-108">이 격리를 통해 구성 요소에 대 한 부적합 한 전체 공격 범주가 렌더링 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be677-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![목적 다이어그램 예제](purpose-strings/_static/purposes.png)

<span data-ttu-id="be677-110">위의 다이어그램에서 `IDataProtector` A와 B 인스턴스는 서로의 페이로드를 읽을 **수 없습니다** .</span><span class="sxs-lookup"><span data-stu-id="be677-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="be677-111">용도 문자열은 비밀로 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="be677-112">잘 작동 하는 다른 구성 요소는 동일한 용도 문자열을 제공 하지 않는다는 점에서 고유 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be677-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="be677-113">데이터 보호 Api를 사용 하는 구성 요소의 네임 스페이스 및 형식 이름을 사용 하는 것이 좋은 방법입니다. 실제로이 정보는 충돌 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="be677-114">Minting 전달자 토큰을 담당 하는 Contoso에서 작성 한 구성 요소는 목적 문자열로 BearerToken을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="be677-115">또는 더 잘 사용 가능 합니다. BearerToken를 용도 문자열로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="be677-116">버전 번호를 추가 하면 이후 버전에서 BearerToken를 목적으로 사용할 수 있으며, 다른 버전은 페이로드 이동 처럼 완전히 격리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be677-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="be677-117">에 대 한 용도 매개 변수가 문자열 배열 이기 때문에 `CreateProtector` 위의은 대신로 지정할 수 있습니다 `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="be677-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="be677-118">이를 통해 용도 계층을 설정 하 고 데이터 보호 시스템으로 다중 테 넌 트 시나리오의 가능성을 열 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="be677-119">구성 요소는 신뢰할 수 없는 사용자 입력이 용도 체인의 유일한 입력 원본인 것을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="be677-120">예를 들어 보안 메시지의 저장을 담당 하는 구성 요소 Contoso.com 메시지를 생각해 보세요.</span><span class="sxs-lookup"><span data-stu-id="be677-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="be677-121">보안 메시징 구성 요소가를 호출 하는 경우 `CreateProtector([ username ])` 악의적인 사용자가 호출 하는 구성 요소를 가져오기 위해 "BearerToken" 라는 사용자 이름이 있는 계정을 만들 수 있습니다 `CreateProtector([ "Contoso.Security.BearerToken" ])` . 따라서 보안 메시징 시스템은 인증 토큰으로 인식 될 수 있는 mint 페이로드의으로 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="be677-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="be677-122">메시징 구성 요소에 대 한 더 나은 용도 체인은 `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` 적절 한 격리를 제공 하는입니다.</span><span class="sxs-lookup"><span data-stu-id="be677-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="be677-123">, 및의 동작에서 제공 하는 격리는 다음과 같습니다 `IDataProtectionProvider` `IDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="be677-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="be677-124">지정 된 개체에 대해 `IDataProtectionProvider` 메서드는 개체를 `CreateProtector` `IDataProtector` `IDataProtectionProvider` 만든 개체와 메서드에 전달 된 용도 매개 변수 모두에 고유 하 게 연결 된 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="be677-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="be677-125">목적 매개 변수는 null이 아니어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be677-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="be677-126">(용도가 배열로 지정 된 경우 배열의 길이가 0이 아니어야 하 고 배열의 모든 요소가 null이 아니어야 함을 의미 합니다.) 빈 문자열 용도는 기술적으로 허용 되지만 권장 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="be677-127">두 가지 용도 인수는 동일한 순서로 동일한 문자열을 포함 하는 경우에만 동일 합니다 (서 수 비교자 사용).</span><span class="sxs-lookup"><span data-stu-id="be677-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="be677-128">단일 용도의 인수는 해당 하는 단일 요소 용도의 배열과 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="be677-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="be677-129">두 `IDataProtector` 개체는 동일한 용도 매개 변수를 사용 하 여 동일한 개체에서 만들어진 경우에만 동일 `IDataProtectionProvider` 합니다.</span><span class="sxs-lookup"><span data-stu-id="be677-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="be677-130">지정 된 개체의 경우에 대 한 `IDataProtector` 호출은 `Unprotect(protectedData)` `unprotectedData` `protectedData := Protect(unprotectedData)` 해당 개체에 대 한 인 경우에만 원래를 반환 합니다 `IDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="be677-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="be677-131">일부 구성 요소는 다른 구성 요소와 충돌 하는 것으로 알려진 목적 문자열을 의도적으로 선택 하는 경우를 고려 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be677-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="be677-132">이러한 구성 요소는 기본적으로 악의적인 것으로 간주 되며,이 시스템은 악의적인 코드가 작업자 프로세스 내에서 이미 실행 되 고 있는 경우 보안을 보장 하기 위한 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="be677-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
