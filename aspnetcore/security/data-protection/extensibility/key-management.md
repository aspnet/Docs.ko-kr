---
title: ASP.NET Core의 키 관리 확장성
author: rick-anderson
description: ASP.NET Core Data Protection key management 확장성에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc, seodec18
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060770"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="e5572-103">ASP.NET Core의 키 관리 확장성</span><span class="sxs-lookup"><span data-stu-id="e5572-103">Key management extensibility in ASP.NET Core</span></span>

<span data-ttu-id="e5572-104">이러한 Api의 기본 개념 중 일부를 설명 하므로이 섹션을 읽기 전에 [키 관리](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e5572-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

<span data-ttu-id="e5572-105">**경고** : 다음 인터페이스를 구현 하는 형식은 여러 호출자에 대해 스레드로부터 안전 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-105">**Warning** : Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="e5572-106">키</span><span class="sxs-lookup"><span data-stu-id="e5572-106">Key</span></span>

<span data-ttu-id="e5572-107">`IKey`인터페이스는 cryptosystem에서 키의 기본 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="e5572-108">용어 키는 "암호화 키 자료"의 리터럴이 아니라 추상적인 의미에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="e5572-109">키에는 다음과 같은 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-109">A key has the following properties:</span></span>

* <span data-ttu-id="e5572-110">활성화, 생성 및 만료 날짜</span><span class="sxs-lookup"><span data-stu-id="e5572-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="e5572-111">해지 상태</span><span class="sxs-lookup"><span data-stu-id="e5572-111">Revocation status</span></span>

* <span data-ttu-id="e5572-112">키 식별자 (GUID)</span><span class="sxs-lookup"><span data-stu-id="e5572-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e5572-113">또한는 `IKey` `CreateEncryptor` 이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="e5572-114">또한는 `IKey` `CreateEncryptorInstance` 이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e5572-115">인스턴스에서 원시 암호화 자료를 검색 하는 API는 없습니다 `IKey` .</span><span class="sxs-lookup"><span data-stu-id="e5572-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="e5572-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="e5572-116">IKeyManager</span></span>

<span data-ttu-id="e5572-117">`IKeyManager`인터페이스는 일반적인 키 저장, 검색 및 조작을 담당 하는 개체를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="e5572-118">3 가지 상위 수준 작업을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="e5572-119">새 키를 만들고 저장소에 보관 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="e5572-120">저장소에서 모든 키를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-120">Get all keys from storage.</span></span>

* <span data-ttu-id="e5572-121">하나 이상의 키를 해지 하 고 저장소에 대 한 해지 정보를 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="e5572-122">을 작성 하는 `IKeyManager` 작업은 매우 고급 작업 이므로 대부분의 개발자는이 작업을 시도해 서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="e5572-123">대신, 대부분의 개발자는 [Xmlkeymanager](#xmlkeymanager) 클래스에서 제공 하는 기능을 활용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="e5572-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="e5572-124">XmlKeyManager</span></span>

<span data-ttu-id="e5572-125">`XmlKeyManager`형식은의 기본 구체적 구현입니다 `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="e5572-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="e5572-126">휴지 상태의 키 에스크로 및 암호화를 포함 하 여 몇 가지 유용한 기능을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="e5572-127">이 시스템의 키는 XML 요소로 표시 됩니다 (특히 [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="e5572-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="e5572-128">`XmlKeyManager` 는 작업을 수행 하는 과정에서 여러 다른 구성 요소에 종속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="e5572-129">`AlgorithmConfiguration`-새 키에 사용 되는 알고리즘을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="e5572-130">`IXmlRepository`-키가 저장소에 유지 되는 위치를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="e5572-131">`IXmlEncryptor` [선택 사항]-미사용 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="e5572-132">`IKeyEscrowSink` [선택 사항]-주요 에스크로 서비스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="e5572-133">`IXmlRepository`-키가 저장소에 유지 되는 위치를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="e5572-134">`IXmlEncryptor` [선택 사항]-미사용 키를 암호화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="e5572-135">`IKeyEscrowSink` [선택 사항]-주요 에스크로 서비스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="e5572-136">다음은 이러한 구성 요소를 내에서 연결 하는 방법을 나타내는 개략적인 다이어그램입니다 `XmlKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="e5572-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation2.png)

<span data-ttu-id="e5572-138">*키 생성/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="e5572-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="e5572-139">의 구현에서는 `CreateNewKey` `AlgorithmConfiguration` 구성 요소를 사용 하 여 고유 `IAuthenticatedEncryptorDescriptor` 를 만든 다음 XML로 serialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="e5572-140">키 에스크로 싱크가 있으면 장기 저장을 위해 원시 (암호화 되지 않은) XML이 싱크에 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="e5572-141">암호화 되지 않은 XML은 `IXmlEncryptor` (필요한 경우)를 통해 실행 되어 암호화 된 xml 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="e5572-142">이 암호화 된 문서는를 통해 장기 저장소에 유지 됩니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="e5572-143">가 구성 되지 않은 경우 암호화 되지 않은 `IXmlEncryptor` 문서는에 유지 됩니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![키 검색](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation1.png)

<span data-ttu-id="e5572-146">*키 생성/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="e5572-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="e5572-147">의 구현에서는 `CreateNewKey` `IAuthenticatedEncryptorConfiguration` 구성 요소를 사용 하 여 고유 `IAuthenticatedEncryptorDescriptor` 를 만든 다음 XML로 serialize 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="e5572-148">키 에스크로 싱크가 있으면 장기 저장을 위해 원시 (암호화 되지 않은) XML이 싱크에 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="e5572-149">암호화 되지 않은 XML은 `IXmlEncryptor` (필요한 경우)를 통해 실행 되어 암호화 된 xml 문서를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="e5572-150">이 암호화 된 문서는를 통해 장기 저장소에 유지 됩니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="e5572-151">가 구성 되지 않은 경우 암호화 되지 않은 `IXmlEncryptor` 문서는에 유지 됩니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![키 검색](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="e5572-153">*키 검색/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="e5572-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="e5572-154">의 구현에서는 `GetAllKeys` 키와 해지를 나타내는 XML 문서를 기본에서 읽습니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="e5572-155">이러한 문서가 암호화 된 경우 시스템에서 자동으로 암호를 해독 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="e5572-156">`XmlKeyManager``IAuthenticatedEncryptorDescriptorDeserializer`문서를 다시 인스턴스로 deserialize 하는 적절 한 인스턴스 `IAuthenticatedEncryptorDescriptor` 를 만들어 개별 `IKey` 인스턴스에 래핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="e5572-157">이 인스턴스의 컬렉션 `IKey` 은 호출자에 게 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="e5572-158">특정 XML 요소에 대 한 자세한 내용은 [키 저장소 형식 문서](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="e5572-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-159">IXmlRepository</span></span>

<span data-ttu-id="e5572-160">`IXmlRepository`인터페이스는 백업 저장소에서 xml을 저장 하 고 xml을 검색할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="e5572-161">다음 두 가지 Api를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-161">It exposes two APIs:</span></span>

* <span data-ttu-id="e5572-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="e5572-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="e5572-163">구현 `IXmlRepository` 에서는 XML을 전달 하는 XML을 구문 분석할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="e5572-164">XML 문서를 불투명 하 게 처리 하 고 더 높은 계층에서 문서 생성 및 구문 분석에 대해 걱정할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="e5572-165">을 구현 하는 네 가지 기본 제공 구체적인 형식이 있습니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="e5572-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="e5572-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="e5572-168">AzureStorage AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="e5572-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="e5572-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="e5572-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="e5572-172">AzureStorage AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="e5572-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="e5572-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="e5572-174">자세한 내용은 [키 저장소 공급자 문서](xref:security/data-protection/implementation/key-storage-providers) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e5572-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="e5572-175">`IXmlRepository`다른 백업 저장소 (예: Azure Table Storage)를 사용 하는 경우 사용자 지정을 등록 하는 것이 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="e5572-176">기본 리포지토리 응용 프로그램 전체를 변경 하려면 사용자 지정 인스턴스를 등록 합니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="e5572-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e5572-177">IXmlEncryptor</span></span>

<span data-ttu-id="e5572-178">`IXmlEncryptor`인터페이스는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="e5572-179">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-179">It exposes a single API:</span></span>

* <span data-ttu-id="e5572-180">Encrypt (XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="e5572-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="e5572-181">Serialize 된에 `IAuthenticatedEncryptorDescriptor` "암호화 필요"로 표시 된 요소가 포함 된 경우는 `XmlKeyManager` 구성 된 `IXmlEncryptor` 의 메서드를 통해 해당 요소를 실행 `Encrypt` 하 고 일반 텍스트 요소가 아닌 암호화 된 읽거나 요소를에 유지 합니다 `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="e5572-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="e5572-182">메서드의 출력은 `Encrypt` `EncryptedXmlInfo` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="e5572-183">이 개체는 결과 암호화 된 읽거나 `XElement` 와 `IXmlDecryptor` 해당 요소를 해독 하는 데 사용할 수 있는를 나타내는 형식을 모두 포함 하는 래퍼입니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="e5572-184">을 구현 하는 네 가지 기본 제공 구체적인 형식이 있습니다 `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="e5572-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="e5572-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e5572-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="e5572-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e5572-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="e5572-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e5572-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="e5572-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="e5572-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="e5572-189">자세한 내용은 [휴지 상태의 키 암호화 문서](xref:security/data-protection/implementation/key-encryption-at-rest) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e5572-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="e5572-190">기본 키 암호화 메커니즘 응용 프로그램 전체를 변경 하려면 사용자 지정 인스턴스를 등록 합니다 `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="e5572-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="e5572-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="e5572-191">IXmlDecryptor</span></span>

<span data-ttu-id="e5572-192">`IXmlDecryptor`인터페이스는를 통해 암호화 된 읽거나 된의 암호를 해독 하는 방법을 알고 있는 형식을 나타냅니다 `XElement` `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="e5572-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="e5572-193">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-193">It exposes a single API:</span></span>

* <span data-ttu-id="e5572-194">암호 해독 (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="e5572-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="e5572-195">메서드는에 `Decrypt` 의해 수행 된 암호화를 취소 합니다 `IXmlEncryptor.Encrypt` .</span><span class="sxs-lookup"><span data-stu-id="e5572-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="e5572-196">일반적으로 구체적인 각 `IXmlEncryptor` 구현에는 해당 하는 구체적인 구현이 포함 됩니다 `IXmlDecryptor` .</span><span class="sxs-lookup"><span data-stu-id="e5572-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="e5572-197">을 구현 하는 형식에는 `IXmlDecryptor` 다음 두 가지 공용 생성자 중 하나가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="e5572-198">.ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="e5572-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="e5572-199">.ctor ()</span><span class="sxs-lookup"><span data-stu-id="e5572-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="e5572-200">`IServiceProvider`생성자에 전달 되는은 null 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="e5572-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="e5572-201">IKeyEscrowSink</span></span>

<span data-ttu-id="e5572-202">`IKeyEscrowSink`인터페이스는 중요 한 정보를 에스크로 할 수 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="e5572-203">직렬화 된 설명자에는 암호화 자료와 같은 중요 한 정보가 포함 될 수 있으며,이는 첫 번째 위치의 [IXmlEncryptor](#ixmlencryptor) 유형을 도입 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="e5572-204">그러나 사고 발생과 키 링은 삭제 하거나 손상 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="e5572-205">에스크로 인터페이스는 구성 된 [IXmlEncryptor](#ixmlencryptor)변환 되기 전에 원시 직렬화 된 XML에 대 한 액세스를 허용 하는 비상 이스케이프 해치를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="e5572-206">인터페이스는 단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="e5572-207">Store (Guid keyId, XElement 요소)</span><span class="sxs-lookup"><span data-stu-id="e5572-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="e5572-208">`IKeyEscrowSink`비즈니스 정책과 일관 된 보안 방식으로 제공 된 요소를 처리 하기 위한 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="e5572-209">에스크로 싱크에서 인증서의 개인 키가 위탁 된 알려진 회사 x.509 인증서를 사용 하 여 XML 요소를 암호화 하는 것이 가능 합니다. `CertificateXmlEncryptor` 형식은이를 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="e5572-210">`IKeyEscrowSink`구현도 제공 된 요소를 적절 하 게 유지 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="e5572-211">서버 관리자가 [전역적으로 구성할](xref:security/data-protection/configuration/machine-wide-policy)수 있지만 기본적으로는 에스크로 메커니즘이 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="e5572-212">아래 샘플에 표시 된 것 처럼 메서드를 통해 프로그래밍 방식으로 구성할 수도 있습니다 `IDataProtectionBuilder.AddKeyEscrowSink` .</span><span class="sxs-lookup"><span data-stu-id="e5572-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="e5572-213">`AddKeyEscrowSink`메서드 오버 로드는 `IServiceCollection.AddSingleton` 인스턴스를 `IServiceCollection.AddInstance` 단일 항목 하기 위해 및 오버 로드를 미러링합니다 `IKeyEscrowSink` .</span><span class="sxs-lookup"><span data-stu-id="e5572-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="e5572-214">여러 `IKeyEscrowSink` 인스턴스를 등록 하는 경우 키를 생성 하는 동안 각 인스턴스를 호출 하므로 여러 메커니즘에 동시에 키를 위탁 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="e5572-215">인스턴스에서 자료를 읽을 수 있는 API가 없습니다 `IKeyEscrowSink` .</span><span class="sxs-lookup"><span data-stu-id="e5572-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="e5572-216">이는 신뢰할 수 있는 기관에서 키 자료를 액세스할 수 있도록 하기 위한 것입니다. 즉, 신뢰할 수 있는 기관에서 키 자료를 액세스할 수 있도록 하기 위한 것 이며, 응용 프로그램이 신뢰할 수 있는 기관이 아니기 때문에 자체 위탁 자료에 대 한 액세스 권한이 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="e5572-217">다음 샘플 코드는 `IKeyEscrowSink` "CONTOSODomain Admins"의 멤버만 복구할 수 있도록 키를 위탁 하는를 만들고 등록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="e5572-218">이 샘플을 실행 하려면 도메인에 가입 된 Windows 8/Windows Server 2012 컴퓨터에 있어야 하 고 도메인 컨트롤러는 Windows Server 2012 이상 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5572-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
