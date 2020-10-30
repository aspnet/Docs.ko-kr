---
title: ASP.NET Core에서 데이터 보호 키 관리 및 수명
author: rick-anderson
description: ASP.NET Core의 데이터 보호 키 관리 및 수명에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053009"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a><span data-ttu-id="b6dc1-103">ASP.NET Core에서 데이터 보호 키 관리 및 수명</span><span class="sxs-lookup"><span data-stu-id="b6dc1-103">Data Protection key management and lifetime in ASP.NET Core</span></span>

<span data-ttu-id="b6dc1-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b6dc1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="key-management"></a><span data-ttu-id="b6dc1-105">키 관리</span><span class="sxs-lookup"><span data-stu-id="b6dc1-105">Key management</span></span>

<span data-ttu-id="b6dc1-106">앱은 해당 운영 환경을 검색 하 고 자체에서 키 구성을 처리 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-106">The app attempts to detect its operational environment and handle key configuration on its own.</span></span>

1. <span data-ttu-id="b6dc1-107">앱이 [Azure 앱](https://azure.microsoft.com/services/app-service/)에서 호스트 되는 경우 키는 *%HOME%\ASP.NET\DataProtection-Keys* 폴더에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-107">If the app is hosted in [Azure Apps](https://azure.microsoft.com/services/app-service/), keys are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="b6dc1-108">이 폴더는 네트워크 스토리지에서 지원하고, 앱을 호스트하는 모든 머신에서 동기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-108">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span>
   * <span data-ttu-id="b6dc1-109">저장된 키는 보호되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-109">Keys aren't protected at rest.</span></span>
   * <span data-ttu-id="b6dc1-110">*Dataprotection-Keys* 폴더는 단일 배포 슬롯의 모든 앱 인스턴스에 대 한 키 링을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-110">The *DataProtection-Keys* folder supplies the key ring to all instances of an app in a single deployment slot.</span></span>
   * <span data-ttu-id="b6dc1-111">준비 및 프로덕션과 같은 별도의 배포 슬롯은 키 링을 공유하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-111">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span> <span data-ttu-id="b6dc1-112">스테이징을 프로덕션으로 교환 하거나 A/B 테스트를 사용 하는 경우와 같이 배포 슬롯 간에 교환 하는 경우 데이터 보호를 사용 하는 모든 앱은 이전 슬롯 내에서 키 링을 사용 하 여 저장 된 데이터의 암호를 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-112">When you swap between deployment slots, for example swapping Staging to Production or using A/B testing, any app using Data Protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="b6dc1-113">이를 통해 사용자는 :::no-loc(cookie)::: 데이터 보호를 사용 하 여를 보호 하므로 표준 ASP.NET Core 인증을 사용 하는 앱에서 로그 아웃 됩니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="b6dc1-113">This leads to users being logged out of an app that uses the standard ASP.NET Core :::no-loc(cookie)::: authentication, as it uses Data Protection to protect its :::no-loc(cookie):::s.</span></span> <span data-ttu-id="b6dc1-114">슬롯 독립적 키 링을 원하는 경우 Azure Blob Storage, Azure Key Vault, SQL 저장소 또는 Redis cache와 같은 외부 키 링 공급자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-114">If you desire slot-independent key rings, use an external key ring provider, such as Azure Blob Storage, Azure Key Vault, a SQL store, or Redis cache.</span></span>

1. <span data-ttu-id="b6dc1-115">사용자 프로필을 사용할 수 있는 경우 키가 *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* 폴더에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-115">If the user profile is available, keys are persisted to the *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="b6dc1-116">운영 체제가 Windows 인 경우 키는 암호화 된 상태로 DPAPI를 사용 하 여 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-116">If the operating system is Windows, the keys are encrypted at rest using DPAPI.</span></span>

   <span data-ttu-id="b6dc1-117">앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-117">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="b6dc1-118">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-118">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="b6dc1-119">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-119">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="b6dc1-120">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-120">If keys aren't stored in the user profile directory as expected:</span></span>

   1. <span data-ttu-id="b6dc1-121">*%windir%/system32/inetsrv/config* 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-121">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
   1. <span data-ttu-id="b6dc1-122">*applicationHost.config* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-122">Open the *applicationHost.config* file.</span></span>
   1. <span data-ttu-id="b6dc1-123">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-123">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
   1. <span data-ttu-id="b6dc1-124">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-124">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

1. <span data-ttu-id="b6dc1-125">앱이 IIS에서 호스트 되는 경우 키는 작업자 프로세스 계정에만 해당 되는 특수 레지스트리 키로 HKLM 레지스트리에 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-125">If the app is hosted in IIS, keys are persisted to the HKLM registry in a special registry key that's ACLed only to the worker process account.</span></span> <span data-ttu-id="b6dc1-126">미사용 키는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-126">Keys are encrypted at rest using DPAPI.</span></span>

1. <span data-ttu-id="b6dc1-127">이러한 조건에 일치 하는 항목이 없는 경우 키는 현재 프로세스 외부에서 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-127">If none of these conditions match, keys aren't persisted outside of the current process.</span></span> <span data-ttu-id="b6dc1-128">프로세스가 종료 되 면 생성 된 모든 키가 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-128">When the process shuts down, all generated keys are lost.</span></span>

<span data-ttu-id="b6dc1-129">개발자는 항상 모든 권한을 가지 며, 키가 저장 되는 방법과 위치를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-129">The developer is always in full control and can override how and where keys are stored.</span></span> <span data-ttu-id="b6dc1-130">위의 처음 세 옵션은 ASP.NET **\<machineKey>** 자동 생성 루틴이 과거에 작업 하는 방식과 유사한 대부분의 앱에 대해 좋은 기본값을 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-130">The first three options above should provide good defaults for most apps similar to how the ASP.NET **\<machineKey>** auto-generation routines worked in the past.</span></span> <span data-ttu-id="b6dc1-131">최종, 대체 (fallback) 옵션은 개발자가 키 지 속성을 원할 경우 [구성을](xref:security/data-protection/configuration/overview) 사전에 지정 해야 하는 유일한 시나리오 이지만 이러한 대체는 드문 경우에만 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-131">The final, fallback option is the only scenario that requires the developer to specify [configuration](xref:security/data-protection/configuration/overview) upfront if they want key persistence, but this fallback only occurs in rare situations.</span></span>

<span data-ttu-id="b6dc1-132">Docker 컨테이너에서 호스트 하는 경우 키는 Docker 볼륨 (컨테이너의 수명 이상으로 지속 되는 공유 볼륨 또는 호스트에서 탑재 된 볼륨) 또는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 또는 [Redis](https://redis.io/)와 같은 외부 공급자에 있는 폴더에 유지 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-132">When hosting in a Docker container, keys should be persisted in a folder that's a Docker volume (a shared volume or a host-mounted volume that persists beyond the container's lifetime) or in an external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span> <span data-ttu-id="b6dc1-133">외부 공급자는 앱이 공유 네트워크 볼륨에 액세스할 수 없는 경우 웹 팜 시나리오에도 유용 합니다 (자세한 내용은 [Persistkeystofilesystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 참조).</span><span class="sxs-lookup"><span data-stu-id="b6dc1-133">An external provider is also useful in web farm scenarios if apps can't access a shared network volume (see [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) for more information).</span></span>

> [!WARNING]
> <span data-ttu-id="b6dc1-134">개발자가 위에 설명 된 규칙을 재정의 하 고 특정 키 리포지토리에서 데이터 보호 시스템을 가리키면 미사용 키의 자동 암호화를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-134">If the developer overrides the rules outlined above and points the Data Protection system at a specific key repository, automatic encryption of keys at rest is disabled.</span></span> <span data-ttu-id="b6dc1-135">Rest 보호는 [구성을](xref:security/data-protection/configuration/overview)통해 다시 사용 하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-135">At-rest protection can be re-enabled via [configuration](xref:security/data-protection/configuration/overview).</span></span>

## <a name="key-lifetime"></a><span data-ttu-id="b6dc1-136">키 수명</span><span class="sxs-lookup"><span data-stu-id="b6dc1-136">Key lifetime</span></span>

<span data-ttu-id="b6dc1-137">키의 수명은 기본적으로 90 일입니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-137">Keys have a 90-day lifetime by default.</span></span> <span data-ttu-id="b6dc1-138">키가 만료 되 면 앱은 자동으로 새 키를 생성 하 고 새 키를 활성 키로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-138">When a key expires, the app automatically generates a new key and sets the new key as the active key.</span></span> <span data-ttu-id="b6dc1-139">사용 중지 된 키가 시스템에 남아 있으면 앱이 해당 키로 보호 되는 데이터의 암호를 해독할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-139">As long as retired keys remain on the system, your app can decrypt any data protected with them.</span></span> <span data-ttu-id="b6dc1-140">자세한 내용은 [키 관리](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-140">See [key management](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) for more information.</span></span>

## <a name="default-algorithms"></a><span data-ttu-id="b6dc1-141">기본 알고리즘</span><span class="sxs-lookup"><span data-stu-id="b6dc1-141">Default algorithms</span></span>

<span data-ttu-id="b6dc1-142">사용 되는 기본 페이로드 보호 알고리즘은 기밀성을 위한 AES-256-HMACSHA256 및 정품 인증입니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-142">The default payload protection algorithm used is AES-256-CBC for confidentiality and HMACSHA256 for authenticity.</span></span> <span data-ttu-id="b6dc1-143">90 일 마다 변경 된 512 비트 마스터 키는 페이로드에 따라 이러한 알고리즘에 사용 되는 두 개의 하위 키를 파생 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-143">A 512-bit master key, changed every 90 days, is used to derive the two sub-keys used for these algorithms on a per-payload basis.</span></span> <span data-ttu-id="b6dc1-144">자세한 내용은 [하위 키 파생](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) 을 참조 하십시오.</span><span class="sxs-lookup"><span data-stu-id="b6dc1-144">See [subkey derivation](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b6dc1-145">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b6dc1-145">Additional resources</span></span>

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
