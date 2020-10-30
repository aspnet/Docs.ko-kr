---
title: ASP.NET Core의 핵심 암호화 확장성
author: rick-anderson
description: IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer 및 최상위 팩터리에 대해 알아보세요.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060783"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>ASP.NET Core의 핵심 암호화 확장성

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> 다음 인터페이스 중 하나를 구현 하는 형식은 여러 호출자에 대해 스레드로부터 안전 해야 합니다.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

**IAuthenticatedEncryptor** 인터페이스는 암호화 하위 시스템의 기본 구성 요소입니다. 일반적으로 키 당 하나의 IAuthenticatedEncryptor가 있으며, IAuthenticatedEncryptor 인스턴스는 암호화 작업을 수행 하는 데 필요한 모든 암호화 키 자료와 알고리즘 정보를 래핑합니다.

이름에서 알 수 있듯이 형식은 인증 된 암호화 및 암호 해독 서비스를 제공 합니다. 다음 두 가지 Api를 노출 합니다.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Encrypt 메서드는 암호화 된 읽거나 일반 텍스트 및 인증 태그를 포함 하는 blob을 반환 합니다. AAD 자체는 최종 페이로드에서 복구 하지 않아도 되지만 인증 태그는 AAD (추가 인증 데이터)를 포함 해야 합니다. 암호 해독 메서드는 인증 태그의 유효성을 검사 하 고 암호 해독 페이로드를 반환 합니다. 모든 오류 (ArgumentNullException 및 비슷함 제외)가 System.security.cryptography.cryptographicexception로 homogenized 되어야 합니다.

> [!NOTE]
> IAuthenticatedEncryptor 인스턴스 자체는 실제로 키 자료를 포함할 필요가 없습니다. 예를 들어 구현은 모든 작업에 대해 HSM에 위임할 수 있습니다.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>IAuthenticatedEncryptor를 만드는 방법

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorFactory** 인터페이스는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 방법을 알고 있는 형식을 나타냅니다. 해당 API는 다음과 같습니다.

* CreateEncryptorInstance (IKey 키): IAuthenticatedEncryptor

지정 된 IKey 인스턴스의 경우 CreateEncryptorInstance 메서드에 의해 만들어진 모든 인증 된 암호기는 아래 코드 샘플과 같이 동등한 것으로 간주 되어야 합니다.

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

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorDescriptor** 인터페이스는 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 방법을 알고 있는 형식을 나타냅니다. 해당 API는 다음과 같습니다.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

IAuthenticatedEncryptor와 마찬가지로 IAuthenticatedEncryptorDescriptor의 인스턴스는 특정 키 하나를 래핑하는 것으로 간주 됩니다. 즉, 지정 된 IAuthenticatedEncryptorDescriptor 인스턴스의 경우 해당 CreateEncryptorInstance 메서드에서 만든 인증 된 암호기은 아래 코드 샘플과 같이 동등한 것으로 간주 되어야 합니다.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x에만 해당)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**IAuthenticatedEncryptorDescriptor** 인터페이스는 XML로 자체를 내보내는 방법을 알고 있는 형식을 나타냅니다. 해당 API는 다음과 같습니다.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML Serialization

IAuthenticatedEncryptor와 IAuthenticatedEncryptorDescriptor 간의 주요 차이점은 설명자가 암호기를 만들고 유효한 인수를 제공 하는 방법을 알고 있다는 것입니다. 구현에서 System.security.cryptography.symmetricalgorithm 및 KeyedHashAlgorithm를 사용 하는 IAuthenticatedEncryptor을 고려 합니다. 암호기의 작업은 이러한 형식을 사용 하는 것 이지만, 이러한 형식이 제공 된 위치를 반드시 알 필요는 없으므로 응용 프로그램을 다시 시작 하는 경우 자체를 다시 만드는 방법에 대 한 적절 한 설명을 작성할 수 없습니다. 설명자는이를 기반으로 상위 수준으로 작동 합니다. 설명자는 암호기 인스턴스를 만드는 방법을 알고 있습니다. 예를 들어 필요한 알고리즘을 만드는 방법을 알고 있으므로 응용 프로그램을 다시 설정한 후 암호기 인스턴스를 다시 만들 수 있도록 XML 형식으로 해당 정보를 serialize 할 수 있습니다.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

ExportToXml 루틴을 통해 설명자를 직렬화 할 수 있습니다. 이 루틴은 두 개의 속성을 포함 하는 XmlSerializedDescriptorInfo를 반환 합니다. 설명자의 XElement 표현과 해당 XElement에서이 설명자를 교체 하는 데 사용할 수 있는 [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) 를 나타내는 형식입니다.

직렬화 된 설명자에는 암호화 키 자료와 같은 중요 한 정보가 포함 될 수 있습니다. 데이터 보호 시스템에는 저장소에 유지 되기 전에 정보를 암호화 하는 기능이 기본적으로 제공 됩니다. 이를 활용 하기 위해 설명자는 특성 이름이 "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> ")이 고 값이 "true" 인 중요 한 정보를 포함 하는 요소를 표시 해야 합니다.

>[!TIP]
> 이 특성을 설정 하기 위한 도우미 API가 있습니다. XElement 네임 스페이스 Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel에 있는 확장 메서드 MarkAsRequiresEncryption ()를 호출 합니다.

Serialize 된 설명자가 중요 한 정보를 포함 하지 않는 경우도 있습니다. HSM에 저장 된 암호화 키의 경우 다시 고려 합니다. HSM은 일반 텍스트 형식으로 자료를 노출 하지 않으므로 설명자는 자신을 serialize 할 때 키 자료를 쓸 수 없습니다. 대신, 설명자는 키의 키 래핑 버전 (HSM이 이런 방식으로 내보내기를 허용 하는 경우) 또는 키에 대 한 HSM의 고유 식별자를 작성할 수 있습니다.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

**IAuthenticatedEncryptorDescriptorDeserializer** 인터페이스는 XElement에서 IAuthenticatedEncryptorDescriptor 인스턴스를 deserialize 하는 방법을 알고 있는 형식을 나타냅니다. 단일 메서드를 노출 합니다.

* ImportFromXml (XElement 요소): IAuthenticatedEncryptorDescriptor

ImportFromXml 메서드는 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) 에서 반환 된 XElement를 사용 하 여 원래 IAuthenticatedEncryptorDescriptor와 동일한을 만듭니다.

IAuthenticatedEncryptorDescriptorDeserializer을 구현 하는 형식에는 다음 두 개의 공용 생성자 중 하나가 있어야 합니다.

* .ctor (IServiceProvider)

* .ctor ()

> [!NOTE]
> 생성자에 전달 된 IServiceProvider는 null 일 수 있습니다.

## <a name="the-top-level-factory"></a>최상위 팩터리

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

**AlgorithmConfiguration** 클래스는 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스를 만드는 방법을 알고 있는 형식을 나타냅니다. 단일 API를 노출 합니다.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

AlgorithmConfiguration을 최상위 팩터리로 간주 합니다. 구성은 템플릿으로 사용 됩니다. 알고리즘 정보를 래핑합니다 (예:이 구성은 AES-128-GCM 마스터 키로 설명자를 생성 함) 하지만 아직 특정 키와 연결 되어 있지 않습니다.

CreateNewDescriptor를 호출 하면 새로운 키 자료가이 호출에 대해서만 생성 되 고,이 키 자료와 자료를 사용 하는 데 필요한 알고리즘 정보를 래핑하는 새 IAuthenticatedEncryptorDescriptor이 생성 됩니다. 키 자료는 소프트웨어에서 만들고 메모리에 보관 될 수 있으며, HSM 내에서 생성 되 고 유지 될 수 있습니다. 중요 한 점은 CreateNewDescriptor에 대 한 두 번의 호출은 동일한 IAuthenticatedEncryptorDescriptor 인스턴스를 만들 수 없다는 것입니다.

AlgorithmConfiguration 형식은 [자동 키 롤링](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)과 같은 키 생성 루틴에 대 한 진입점으로 사용 됩니다. 이후 모든 키에 대 한 구현을 변경 하려면 KeyManagementOptions에서 AuthenticatedEncryptorConfiguration 속성을 설정 합니다.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

**IAuthenticatedEncryptorConfiguration** 인터페이스는 [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) 인스턴스를 만드는 방법을 알고 있는 유형을 나타냅니다. 단일 API를 노출 합니다.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

IAuthenticatedEncryptorConfiguration을 최상위 팩터리로 간주 합니다. 구성은 템플릿으로 사용 됩니다. 알고리즘 정보를 래핑합니다 (예:이 구성은 AES-128-GCM 마스터 키로 설명자를 생성 함) 하지만 아직 특정 키와 연결 되어 있지 않습니다.

CreateNewDescriptor를 호출 하면 새로운 키 자료가이 호출에 대해서만 생성 되 고,이 키 자료와 자료를 사용 하는 데 필요한 알고리즘 정보를 래핑하는 새 IAuthenticatedEncryptorDescriptor이 생성 됩니다. 키 자료는 소프트웨어에서 만들고 메모리에 보관 될 수 있으며, HSM 내에서 생성 되 고 유지 될 수 있습니다. 중요 한 점은 CreateNewDescriptor에 대 한 두 번의 호출은 동일한 IAuthenticatedEncryptorDescriptor 인스턴스를 만들 수 없다는 것입니다.

IAuthenticatedEncryptorConfiguration 형식은 [자동 키 롤링](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)과 같은 키 생성 루틴에 대 한 진입점으로 사용 됩니다. 이후 모든 키에 대 한 구현을 변경 하려면 서비스 컨테이너에 단일 항목 IAuthenticatedEncryptorConfiguration를 등록 합니다.

---
