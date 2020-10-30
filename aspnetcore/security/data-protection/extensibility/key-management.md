---
title: ASP.NET Core의 키 관리 확장성
author: rick-anderson
description: ASP.NET Core Data Protection key management 확장성에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060770"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core의 키 관리 확장성

이러한 Api의 기본 개념 중 일부를 설명 하므로이 섹션을 읽기 전에 [키 관리](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 섹션을 참조 하세요.

**경고** : 다음 인터페이스를 구현 하는 형식은 여러 호출자에 대해 스레드로부터 안전 해야 합니다.

## <a name="key"></a>키

`IKey`인터페이스는 cryptosystem에서 키의 기본 표현입니다. 용어 키는 "암호화 키 자료"의 리터럴이 아니라 추상적인 의미에서 사용 됩니다. 키에는 다음과 같은 속성이 있습니다.

* 활성화, 생성 및 만료 날짜

* 해지 상태

* 키 식별자 (GUID)

::: moniker range=">= aspnetcore-2.0"

또한는 `IKey` `CreateEncryptor` 이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 메서드를 노출 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

또한는 `IKey` `CreateEncryptorInstance` 이 키에 연결 된 [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) 인스턴스를 만드는 데 사용할 수 있는 메서드를 노출 합니다.

::: moniker-end

> [!NOTE]
> 인스턴스에서 원시 암호화 자료를 검색 하는 API는 없습니다 `IKey` .

## <a name="ikeymanager"></a>IKeyManager

`IKeyManager`인터페이스는 일반적인 키 저장, 검색 및 조작을 담당 하는 개체를 나타냅니다. 3 가지 상위 수준 작업을 노출 합니다.

* 새 키를 만들고 저장소에 보관 합니다.

* 저장소에서 모든 키를 가져옵니다.

* 하나 이상의 키를 해지 하 고 저장소에 대 한 해지 정보를 유지 합니다.

>[!WARNING]
> 을 작성 하는 `IKeyManager` 작업은 매우 고급 작업 이므로 대부분의 개발자는이 작업을 시도해 서는 안 됩니다. 대신, 대부분의 개발자는 [Xmlkeymanager](#xmlkeymanager) 클래스에서 제공 하는 기능을 활용 해야 합니다.

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager`형식은의 기본 구체적 구현입니다 `IKeyManager` . 휴지 상태의 키 에스크로 및 암호화를 포함 하 여 몇 가지 유용한 기능을 제공 합니다. 이 시스템의 키는 XML 요소로 표시 됩니다 (특히 [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` 는 작업을 수행 하는 과정에서 여러 다른 구성 요소에 종속 됩니다.

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`-새 키에 사용 되는 알고리즘을 나타냅니다.

* `IXmlRepository`-키가 저장소에 유지 되는 위치를 제어 합니다.

* `IXmlEncryptor` [선택 사항]-미사용 키를 암호화할 수 있습니다.

* `IKeyEscrowSink` [선택 사항]-주요 에스크로 서비스를 제공 합니다.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`-키가 저장소에 유지 되는 위치를 제어 합니다.

* `IXmlEncryptor` [선택 사항]-미사용 키를 암호화할 수 있습니다.

* `IKeyEscrowSink` [선택 사항]-주요 에스크로 서비스를 제공 합니다.

::: moniker-end

다음은 이러한 구성 요소를 내에서 연결 하는 방법을 나타내는 개략적인 다이어그램입니다 `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation2.png)

*키 생성/CreateNewKey*

의 구현에서는 `CreateNewKey` `AlgorithmConfiguration` 구성 요소를 사용 하 여 고유 `IAuthenticatedEncryptorDescriptor` 를 만든 다음 XML로 serialize 합니다. 키 에스크로 싱크가 있으면 장기 저장을 위해 원시 (암호화 되지 않은) XML이 싱크에 제공 됩니다. 암호화 되지 않은 XML은 `IXmlEncryptor` (필요한 경우)를 통해 실행 되어 암호화 된 xml 문서를 생성 합니다. 이 암호화 된 문서는를 통해 장기 저장소에 유지 됩니다 `IXmlRepository` . 가 구성 되지 않은 경우 암호화 되지 않은 `IXmlEncryptor` 문서는에 유지 됩니다 `IXmlRepository` .

![키 검색](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![키 만들기](key-management/_static/keycreation1.png)

*키 생성/CreateNewKey*

의 구현에서는 `CreateNewKey` `IAuthenticatedEncryptorConfiguration` 구성 요소를 사용 하 여 고유 `IAuthenticatedEncryptorDescriptor` 를 만든 다음 XML로 serialize 합니다. 키 에스크로 싱크가 있으면 장기 저장을 위해 원시 (암호화 되지 않은) XML이 싱크에 제공 됩니다. 암호화 되지 않은 XML은 `IXmlEncryptor` (필요한 경우)를 통해 실행 되어 암호화 된 xml 문서를 생성 합니다. 이 암호화 된 문서는를 통해 장기 저장소에 유지 됩니다 `IXmlRepository` . 가 구성 되지 않은 경우 암호화 되지 않은 `IXmlEncryptor` 문서는에 유지 됩니다 `IXmlRepository` .

![키 검색](key-management/_static/keyretrieval1.png)

::: moniker-end

*키 검색/GetAllKeys*

의 구현에서는 `GetAllKeys` 키와 해지를 나타내는 XML 문서를 기본에서 읽습니다 `IXmlRepository` . 이러한 문서가 암호화 된 경우 시스템에서 자동으로 암호를 해독 합니다. `XmlKeyManager``IAuthenticatedEncryptorDescriptorDeserializer`문서를 다시 인스턴스로 deserialize 하는 적절 한 인스턴스 `IAuthenticatedEncryptorDescriptor` 를 만들어 개별 `IKey` 인스턴스에 래핑됩니다. 이 인스턴스의 컬렉션 `IKey` 은 호출자에 게 반환 됩니다.

특정 XML 요소에 대 한 자세한 내용은 [키 저장소 형식 문서](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)에서 찾을 수 있습니다.

## <a name="ixmlrepository"></a>IXmlRepository

`IXmlRepository`인터페이스는 백업 저장소에서 xml을 저장 하 고 xml을 검색할 수 있는 형식을 나타냅니다. 다음 두 가지 Api를 노출 합니다.

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

구현 `IXmlRepository` 에서는 XML을 전달 하는 XML을 구문 분석할 필요가 없습니다. XML 문서를 불투명 하 게 처리 하 고 더 높은 계층에서 문서 생성 및 구문 분석에 대해 걱정할 수 있어야 합니다.

을 구현 하는 네 가지 기본 제공 구체적인 형식이 있습니다 `IXmlRepository` .

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

자세한 내용은 [키 저장소 공급자 문서](xref:security/data-protection/implementation/key-storage-providers) 를 참조 하세요.

`IXmlRepository`다른 백업 저장소 (예: Azure Table Storage)를 사용 하는 경우 사용자 지정을 등록 하는 것이 적합 합니다.

기본 리포지토리 응용 프로그램 전체를 변경 하려면 사용자 지정 인스턴스를 등록 합니다 `IXmlRepository` .

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

`IXmlEncryptor`인터페이스는 일반 텍스트 XML 요소를 암호화할 수 있는 형식을 나타냅니다. 단일 API를 노출 합니다.

* Encrypt (XElement plaintextElement): EncryptedXmlInfo

Serialize 된에 `IAuthenticatedEncryptorDescriptor` "암호화 필요"로 표시 된 요소가 포함 된 경우는 `XmlKeyManager` 구성 된 `IXmlEncryptor` 의 메서드를 통해 해당 요소를 실행 `Encrypt` 하 고 일반 텍스트 요소가 아닌 암호화 된 읽거나 요소를에 유지 합니다 `IXmlRepository` . 메서드의 출력은 `Encrypt` `EncryptedXmlInfo` 개체입니다. 이 개체는 결과 암호화 된 읽거나 `XElement` 와 `IXmlDecryptor` 해당 요소를 해독 하는 데 사용할 수 있는를 나타내는 형식을 모두 포함 하는 래퍼입니다.

을 구현 하는 네 가지 기본 제공 구체적인 형식이 있습니다 `IXmlEncryptor` .

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

자세한 내용은 [휴지 상태의 키 암호화 문서](xref:security/data-protection/implementation/key-encryption-at-rest) 를 참조 하세요.

기본 키 암호화 메커니즘 응용 프로그램 전체를 변경 하려면 사용자 지정 인스턴스를 등록 합니다 `IXmlEncryptor` .

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

`IXmlDecryptor`인터페이스는를 통해 암호화 된 읽거나 된의 암호를 해독 하는 방법을 알고 있는 형식을 나타냅니다 `XElement` `IXmlEncryptor` . 단일 API를 노출 합니다.

* 암호 해독 (XElement encryptedElement): XElement

메서드는에 `Decrypt` 의해 수행 된 암호화를 취소 합니다 `IXmlEncryptor.Encrypt` . 일반적으로 구체적인 각 `IXmlEncryptor` 구현에는 해당 하는 구체적인 구현이 포함 됩니다 `IXmlDecryptor` .

을 구현 하는 형식에는 `IXmlDecryptor` 다음 두 가지 공용 생성자 중 하나가 있어야 합니다.

* .ctor (IServiceProvider)
* .ctor ()

> [!NOTE]
> `IServiceProvider`생성자에 전달 되는은 null 일 수 있습니다.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

`IKeyEscrowSink`인터페이스는 중요 한 정보를 에스크로 할 수 있는 형식을 나타냅니다. 직렬화 된 설명자에는 암호화 자료와 같은 중요 한 정보가 포함 될 수 있으며,이는 첫 번째 위치의 [IXmlEncryptor](#ixmlencryptor) 유형을 도입 하는 것입니다. 그러나 사고 발생과 키 링은 삭제 하거나 손상 될 수 있습니다.

에스크로 인터페이스는 구성 된 [IXmlEncryptor](#ixmlencryptor)변환 되기 전에 원시 직렬화 된 XML에 대 한 액세스를 허용 하는 비상 이스케이프 해치를 제공 합니다. 인터페이스는 단일 API를 노출 합니다.

* Store (Guid keyId, XElement 요소)

`IKeyEscrowSink`비즈니스 정책과 일관 된 보안 방식으로 제공 된 요소를 처리 하기 위한 구현입니다. 에스크로 싱크에서 인증서의 개인 키가 위탁 된 알려진 회사 x.509 인증서를 사용 하 여 XML 요소를 암호화 하는 것이 가능 합니다. `CertificateXmlEncryptor` 형식은이를 지원할 수 있습니다. `IKeyEscrowSink`구현도 제공 된 요소를 적절 하 게 유지 하는 일을 담당 합니다.

서버 관리자가 [전역적으로 구성할](xref:security/data-protection/configuration/machine-wide-policy)수 있지만 기본적으로는 에스크로 메커니즘이 사용 되지 않습니다. 아래 샘플에 표시 된 것 처럼 메서드를 통해 프로그래밍 방식으로 구성할 수도 있습니다 `IDataProtectionBuilder.AddKeyEscrowSink` . `AddKeyEscrowSink`메서드 오버 로드는 `IServiceCollection.AddSingleton` 인스턴스를 `IServiceCollection.AddInstance` 단일 항목 하기 위해 및 오버 로드를 미러링합니다 `IKeyEscrowSink` . 여러 `IKeyEscrowSink` 인스턴스를 등록 하는 경우 키를 생성 하는 동안 각 인스턴스를 호출 하므로 여러 메커니즘에 동시에 키를 위탁 수 있습니다.

인스턴스에서 자료를 읽을 수 있는 API가 없습니다 `IKeyEscrowSink` . 이는 신뢰할 수 있는 기관에서 키 자료를 액세스할 수 있도록 하기 위한 것입니다. 즉, 신뢰할 수 있는 기관에서 키 자료를 액세스할 수 있도록 하기 위한 것 이며, 응용 프로그램이 신뢰할 수 있는 기관이 아니기 때문에 자체 위탁 자료에 대 한 액세스 권한이 없어야 합니다.

다음 샘플 코드는 `IKeyEscrowSink` "CONTOSODomain Admins"의 멤버만 복구할 수 있도록 키를 위탁 하는를 만들고 등록 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 샘플을 실행 하려면 도메인에 가입 된 Windows 8/Windows Server 2012 컴퓨터에 있어야 하 고 도메인 컨트롤러는 Windows Server 2012 이상 이어야 합니다.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
