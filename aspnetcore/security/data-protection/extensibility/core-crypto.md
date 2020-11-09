---
title: ASP.NET Core의 핵심 암호화 확장성
author: rick-anderson
description: IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer 및 최상위 팩터리에 대해 알아보세요.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060783"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="6dad1-103">ASP.NET Core의 핵심 암호화 확장성</span><span class="sxs-lookup"><span data-stu-id="6dad1-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="6dad1-104">다음 인터페이스 중 하나를 구현 하는 형식은 여러 호출자에 대해 스레드로부터 안전 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="6dad1-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="6dad1-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="6dad1-106">**IAuthenticatedEncryptor** 인터페이스는 암호화 하위 시스템의 기본 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="6dad1-107">일반적으로 키 당 하나의 IAuthenticatedEncryptor가 있으며, IAuthenticatedEncryptor 인스턴스는 암호화 작업을 수행 하는 데 필요한 모든 암호화 키 자료와 알고리즘 정보를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="6dad1-108">이름에서 알 수 있듯이 형식은 인증 된 암호화 및 암호 해독 서비스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="6dad1-109">다음 두 가지 Api를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="6dad1-110">Encrypt 메서드는 암호화 된 읽거나 일반 텍스트 및 인증 태그를 포함 하는 blob을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="6dad1-111">AAD 자체는 최종 페이로드에서 복구 하지 않아도 되지만 인증 태그는 AAD (추가 인증 데이터)를 포함 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="6dad1-112">암호 해독 메서드는 인증 태그의 유효성을 검사 하 고 암호 해독 페이로드를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="6dad1-113">모든 오류 (ArgumentNullException 및 비슷함 제외)가 System.security.cryptography.cryptographicexception로 homogenized 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="6dad1-114">IAuthenticatedEncryptor 인스턴스 자체는 실제로 키 자료를 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="6dad1-115">예를 들어 구현은 모든 작업에 대해 HSM에 위임할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="6dad1-116">IAuthenticatedEncryptor를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="6dad1-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="6dad1-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6dad1-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="6dad1-118">**IAuthenticatedEncryptorFactory** 인터페이스는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="6dad1-119">해당 API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-119">Its API is as follows.</span></span>

* <span data-ttu-id="6dad1-120">CreateEncryptorInstance (IKey 키): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="6dad1-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="6dad1-121">지정 된 IKey 인스턴스의 경우 CreateEncryptorInstance 메서드에 의해 만들어진 모든 인증 된 암호기는 아래 코드 샘플과 같이 동등한 것으로 간주 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="6dad1-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6dad1-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="6dad1-123">**IAuthenticatedEncryptorDescriptor** 인터페이스는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="6dad1-124">해당 API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-124">Its API is as follows.</span></span>

* <span data-ttu-id="6dad1-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="6dad1-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="6dad1-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="6dad1-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="6dad1-127">IAuthenticatedEncryptor와 마찬가지로 IAuthenticatedEncryptorDescriptor의 인스턴스는 특정 키 하나를 래핑하는 것으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="6dad1-128">즉, 지정 된 IAuthenticatedEncryptorDescriptor 인스턴스의 경우 해당 CreateEncryptorInstance 메서드에서 만든 인증 된 암호기은 아래 코드 샘플과 같이 동등한 것으로 간주 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="6dad1-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x에만 해당)</span><span class="sxs-lookup"><span data-stu-id="6dad1-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="6dad1-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6dad1-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="6dad1-131">**IAuthenticatedEncryptorDescriptor** 인터페이스는 XML로 자체를 내보내는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="6dad1-132">해당 API는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-132">Its API is as follows.</span></span>

* <span data-ttu-id="6dad1-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="6dad1-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="6dad1-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6dad1-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="6dad1-135">XML Serialization</span><span class="sxs-lookup"><span data-stu-id="6dad1-135">XML Serialization</span></span>

<span data-ttu-id="6dad1-136">IAuthenticatedEncryptor와 IAuthenticatedEncryptorDescriptor 간의 주요 차이점은 설명자가 암호기를 만들고 유효한 인수를 제공 하는 방법을 알고 있다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="6dad1-137">구현에서 System.security.cryptography.symmetricalgorithm 및 KeyedHashAlgorithm를 사용 하는 IAuthenticatedEncryptor을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="6dad1-138">암호기의 작업은 이러한 형식을 사용 하는 것 이지만, 이러한 형식이 제공 된 위치를 반드시 알 필요는 없으므로 응용 프로그램을 다시 시작 하는 경우 자체를 다시 만드는 방법에 대 한 적절 한 설명을 작성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="6dad1-139">설명자는이를 기반으로 상위 수준으로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="6dad1-140">설명자는 암호기 인스턴스를 만드는 방법을 알고 있습니다. 예를 들어 필요한 알고리즘을 만드는 방법을 알고 있으므로 응용 프로그램을 다시 설정한 후 암호기 인스턴스를 다시 만들 수 있도록 XML 형식으로 해당 정보를 serialize 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="6dad1-141">ExportToXml 루틴을 통해 설명자를 직렬화 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="6dad1-142">이 루틴은 두 개의 속성을 포함 하는 XmlSerializedDescriptorInfo를 반환 합니다. 설명자의 XElement 표현과 해당 XElement에서이 설명자를 교체 하는 데 사용할 수 있는 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) 를 나타내는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="6dad1-143">직렬화 된 설명자에는 암호화 키 자료와 같은 중요 한 정보가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="6dad1-144">데이터 보호 시스템에는 저장소에 유지 되기 전에 정보를 암호화 하는 기능이 기본적으로 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="6dad1-145">이를 활용 하기 위해 설명자는 특성 이름이 "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> ")이 고 값이 "true" 인 중요 한 정보를 포함 하는 요소를 표시 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="6dad1-146">이 특성을 설정 하기 위한 도우미 API가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="6dad1-147">XElement 네임 스페이스 Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel에 있는 확장 메서드 MarkAsRequiresEncryption ()를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="6dad1-148">Serialize 된 설명자가 중요 한 정보를 포함 하지 않는 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="6dad1-149">HSM에 저장 된 암호화 키의 경우 다시 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="6dad1-150">HSM은 일반 텍스트 형식으로 자료를 노출 하지 않으므로 설명자는 자신을 serialize 할 때 키 자료를 쓸 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="6dad1-151">대신, 설명자는 키의 키 래핑 버전 (HSM이 이런 방식으로 내보내기를 허용 하는 경우) 또는 키에 대 한 HSM의 고유 식별자를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="6dad1-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="6dad1-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="6dad1-153">**IAuthenticatedEncryptorDescriptorDeserializer** 인터페이스는 XElement에서 IAuthenticatedEncryptorDescriptor 인스턴스를 deserialize 하는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="6dad1-154">단일 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-154">It exposes a single method:</span></span>

* <span data-ttu-id="6dad1-155">ImportFromXml (XElement 요소): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="6dad1-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="6dad1-156">ImportFromXml 메서드는 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) 에서 반환 된 XElement를 사용 하 여 원래 IAuthenticatedEncryptorDescriptor와 동일한을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="6dad1-157">IAuthenticatedEncryptorDescriptorDeserializer을 구현 하는 형식에는 다음 두 개의 공용 생성자 중 하나가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="6dad1-158">.ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="6dad1-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="6dad1-159">.ctor ()</span><span class="sxs-lookup"><span data-stu-id="6dad1-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="6dad1-160">생성자에 전달 된 IServiceProvider는 null 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="6dad1-161">최상위 팩터리</span><span class="sxs-lookup"><span data-stu-id="6dad1-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="6dad1-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="6dad1-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="6dad1-163">**AlgorithmConfiguration** 클래스는 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스를 만드는 방법을 알고 있는 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="6dad1-164">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-164">It exposes a single API.</span></span>

* <span data-ttu-id="6dad1-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="6dad1-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="6dad1-166">AlgorithmConfiguration을 최상위 팩터리로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="6dad1-167">구성은 템플릿으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-167">The configuration serves as a template.</span></span> <span data-ttu-id="6dad1-168">알고리즘 정보를 래핑합니다 (예:이 구성은 AES-128-GCM 마스터 키로 설명자를 생성 함) 하지만 아직 특정 키와 연결 되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="6dad1-169">CreateNewDescriptor를 호출 하면 새로운 키 자료가이 호출에 대해서만 생성 되 고,이 키 자료와 자료를 사용 하는 데 필요한 알고리즘 정보를 래핑하는 새 IAuthenticatedEncryptorDescriptor이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="6dad1-170">키 자료는 소프트웨어에서 만들고 메모리에 보관 될 수 있으며, HSM 내에서 생성 되 고 유지 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="6dad1-171">중요 한 점은 CreateNewDescriptor에 대 한 두 번의 호출은 동일한 IAuthenticatedEncryptorDescriptor 인스턴스를 만들 수 없다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="6dad1-172">AlgorithmConfiguration 형식은 [자동 키 롤링](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)과 같은 키 생성 루틴에 대 한 진입점으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="6dad1-173">이후 모든 키에 대 한 구현을 변경 하려면 KeyManagementOptions에서 AuthenticatedEncryptorConfiguration 속성을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="6dad1-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="6dad1-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="6dad1-175">**IAuthenticatedEncryptorConfiguration** 인터페이스는 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스를 만드는 방법을 알고 있는 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="6dad1-176">단일 API를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-176">It exposes a single API.</span></span>

* <span data-ttu-id="6dad1-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="6dad1-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="6dad1-178">IAuthenticatedEncryptorConfiguration을 최상위 팩터리로 간주 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="6dad1-179">구성은 템플릿으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-179">The configuration serves as a template.</span></span> <span data-ttu-id="6dad1-180">알고리즘 정보를 래핑합니다 (예:이 구성은 AES-128-GCM 마스터 키로 설명자를 생성 함) 하지만 아직 특정 키와 연결 되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="6dad1-181">CreateNewDescriptor를 호출 하면 새로운 키 자료가이 호출에 대해서만 생성 되 고,이 키 자료와 자료를 사용 하는 데 필요한 알고리즘 정보를 래핑하는 새 IAuthenticatedEncryptorDescriptor이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="6dad1-182">키 자료는 소프트웨어에서 만들고 메모리에 보관 될 수 있으며, HSM 내에서 생성 되 고 유지 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="6dad1-183">중요 한 점은 CreateNewDescriptor에 대 한 두 번의 호출은 동일한 IAuthenticatedEncryptorDescriptor 인스턴스를 만들 수 없다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="6dad1-184">IAuthenticatedEncryptorConfiguration 형식은 [자동 키 롤링](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)과 같은 키 생성 루틴에 대 한 진입점으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="6dad1-185">이후 모든 키에 대 한 구현을 변경 하려면 서비스 컨테이너에 단일 항목 IAuthenticatedEncryptorConfiguration를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6dad1-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
