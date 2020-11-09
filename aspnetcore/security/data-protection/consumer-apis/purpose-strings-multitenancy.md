---
title: ASP.NET Core의 용도 계층 구조 및 다중 테 넌 트
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api와 관련 된 용도 문자열 계층 구조 및 다중 테 넌 트에 대해 알아봅니다.
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
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 84714cd852a3cbc7ff77d0fd0c88931536dead1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052788"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a><span data-ttu-id="0a643-103">ASP.NET Core의 용도 계층 구조 및 다중 테 넌 트</span><span class="sxs-lookup"><span data-stu-id="0a643-103">Purpose hierarchy and multi-tenancy in ASP.NET Core</span></span>

<span data-ttu-id="0a643-104">는 `IDataProtector` 또한 암시적 이므로 `IDataProtectionProvider` 용도를 함께 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-104">Since an `IDataProtector` is also implicitly an `IDataProtectionProvider`, purposes can be chained together.</span></span> <span data-ttu-id="0a643-105">이 의미에서 `provider.CreateProtector([ "purpose1", "purpose2" ])` 는와 같습니다 `provider.CreateProtector("purpose1").CreateProtector("purpose2")` .</span><span class="sxs-lookup"><span data-stu-id="0a643-105">In this sense, `provider.CreateProtector([ "purpose1", "purpose2" ])` is equivalent to `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.</span></span>

<span data-ttu-id="0a643-106">이를 통해 데이터 보호 시스템을 통해 일부 흥미로운 계층 관계가 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-106">This allows for some interesting hierarchical relationships through the data protection system.</span></span> <span data-ttu-id="0a643-107">이전 예의 [contoso.com](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)메시지에서 securemessage 구성 요소는 `provider.CreateProtector("Contoso.Messaging.SecureMessage")` 한 번만 호출 하 고 결과를 전용 필드에 캐시할 수 있습니다. `_myProvider`</span><span class="sxs-lookup"><span data-stu-id="0a643-107">In the earlier example of [Contoso.Messaging.SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), the SecureMessage component can call `provider.CreateProtector("Contoso.Messaging.SecureMessage")` once up-front and cache the result into a private `_myProvider` field.</span></span> <span data-ttu-id="0a643-108">그런 다음에 대 한 호출을 통해 향후 보호기를 만들 수 `_myProvider.CreateProtector("User: username")` 있으며, 이러한 보호기는 개별 메시지를 보호 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-108">Future protectors can then be created via calls to `_myProvider.CreateProtector("User: username")`, and these protectors would be used for securing the individual messages.</span></span>

<span data-ttu-id="0a643-109">이를 대칭 이동할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-109">This can also be flipped.</span></span> <span data-ttu-id="0a643-110">여러 테 넌 트를 호스팅하는 단일 논리적 응용 프로그램 (CMS가 적절 한 것 같음)을 고려 하 고 각 테 넌 트는 자체 인증 및 상태 관리 시스템을 사용 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-110">Consider a single logical application which hosts multiple tenants (a CMS seems reasonable), and each tenant can be configured with its own authentication and state management system.</span></span> <span data-ttu-id="0a643-111">파라솔 응용 프로그램에는 단일 마스터 공급자가 있으며 `provider.CreateProtector("Tenant 1")` 및를 호출 `provider.CreateProtector("Tenant 2")` 하 여 각 테 넌 트에 데이터 보호 시스템의 고유한 격리 조각을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-111">The umbrella application has a single master provider, and it calls `provider.CreateProtector("Tenant 1")` and `provider.CreateProtector("Tenant 2")` to give each tenant its own isolated slice of the data protection system.</span></span> <span data-ttu-id="0a643-112">그런 다음 테 넌 트는 자체 요구 사항에 따라 자체의 개별 보호기를 파생 시킬 수 있지만, 시스템의 다른 테 넌 트와 충돌 하는 보호기를 만들 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-112">The tenants could then derive their own individual protectors based on their own needs, but no matter how hard they try they cannot create protectors which collide with any other tenant in the system.</span></span> <span data-ttu-id="0a643-113">그래픽으로 표시 되는이는 아래와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-113">Graphically, this is represented as below.</span></span>

![다중 테 넌 시 목적](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> <span data-ttu-id="0a643-115">이는 포괄적인 응용 프로그램이 개별 테 넌 트에 사용할 수 있는 Api를 제어 하 고 테 넌 트가 서버에서 임의 코드를 실행할 수 없다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-115">This assumes the umbrella application controls which APIs are available to individual tenants and that tenants cannot execute arbitrary code on the server.</span></span> <span data-ttu-id="0a643-116">테 넌 트가 임의의 코드를 실행할 수 있는 경우 개인 리플렉션을 수행 하 여 격리 보장을 중단 하거나 마스터 키 자료를 직접 읽고 원하는 하위 키를 파생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-116">If a tenant can execute arbitrary code, they could perform private reflection to break the isolation guarantees, or they could just read the master keying material directly and derive whatever subkeys they desire.</span></span>

<span data-ttu-id="0a643-117">데이터 보호 시스템은 실제로 기본 구성으로 일종의 다중 테 넌 트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-117">The data protection system actually uses a sort of multi-tenancy in its default out-of-the-box configuration.</span></span> <span data-ttu-id="0a643-118">기본적으로 마스터 키 자료는 작업자 프로세스 계정의 사용자 프로필 폴더 (또는 IIS 응용 프로그램 풀 id에 대 한 레지스트리)에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-118">By default master keying material is stored in the worker process account's user profile folder (or the registry, for IIS application pool identities).</span></span> <span data-ttu-id="0a643-119">그러나 실제로는 단일 계정을 사용 하 여 여러 응용 프로그램을 실행 하는 것이 매우 일반적입니다. 따라서 이러한 모든 응용 프로그램은 마스터 키 자료를 공유 하 게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-119">But it's actually fairly common to use a single account to run multiple applications, and thus all these applications would end up sharing the master keying material.</span></span> <span data-ttu-id="0a643-120">이를 해결 하기 위해 데이터 보호 시스템은 전체 용도 체인에서 첫 번째 요소로 고유한 응용 프로그램별 식별자를 자동으로 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-120">To solve this, the data protection system automatically inserts a unique-per-application identifier as the first element in the overall purpose chain.</span></span> <span data-ttu-id="0a643-121">이 암시적 용도는 각 응용 프로그램을 시스템 내의 고유 테 넌 트로 효율적으로 처리 하 여 [개별 응용 프로그램](xref:security/data-protection/configuration/overview#per-application-isolation) 을 서로 격리 하는 데 사용 되며, 보호기 생성 프로세스는 위의 이미지와 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="0a643-121">This implicit purpose serves to [isolate individual applications](xref:security/data-protection/configuration/overview#per-application-isolation) from one another by effectively treating each application as a unique tenant within the system, and the protector creation process looks identical to the image above.</span></span>
