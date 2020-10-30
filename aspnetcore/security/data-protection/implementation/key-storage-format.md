---
title: ASP.NET Core의 키 저장소 형식
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 저장소 형식에 대 한 구현 세부 정보를 알아봅니다.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060146"
---
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NET Core의 키 저장소 형식

<a name="data-protection-implementation-key-storage-format"></a>

개체는 미사용 위치에 XML 표현으로 저장 됩니다. 키 저장소의 기본 디렉터리는 다음과 같습니다.

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS/Linux: *$HOME/.aspnet/dataprotection-keys*

## <a name="the-key-element"></a>\<key> 요소

키가 키 리포지토리에서 최상위 개체로 존재 합니다. 규칙에 따라 키에는 파일 이름 **-{guid} .xml** 이 있습니다. 여기서 {guid}는 키의 id입니다. 이러한 각 파일에는 단일 키가 포함 되어 있습니다. 파일 형식은 다음과 같습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

\<key>요소는 다음 특성 및 자식 요소를 포함 합니다.

* 키 id입니다. 이 값은 권한 있는 것으로 간주 됩니다. filename은 사람이 읽을 수 있는 nicety 뿐입니다.

* \<key>현재 1에서 고정 된 요소의 버전입니다.

* 키의 생성, 활성화 및 만료 날짜입니다.

* \<descriptor>이 키에 포함 된 인증 된 암호화 구현에 대 한 정보를 포함 하는 요소입니다.

위의 예제에서 키의 id는 {80732141-ec8f-af9c-c4d2d1ff8901}이 고 3 월 2015 19 일에 만들어지고 활성화 되었으며 90 일의 수명이 있습니다. (이 예제에서와 같이 활성화 날짜가 만든 날짜 보다 약간 약간 낮을 수 있습니다. 이는 Api가 작동 하는 방식에 사소한 하 고 실제로는 바람직하지 않기 때문입니다.)

## <a name="the-descriptor-element"></a>\<descriptor> 요소

외부 \<descriptor> 요소는 IAuthenticatedEncryptorDescriptorDeserializer를 구현 하는 형식의 어셈블리로 한정 된 이름인 deserializerType 특성을 포함 합니다. 이 형식은 내부 요소를 읽고 내에 \<descriptor> 포함 된 정보를 구문 분석 하는 일을 담당 합니다.

요소의 특정 형식은 \<descriptor> 키로 캡슐화 된 인증 된 암호기 구현에 따라 달라 지 며 각 역직렬 변환기 형식은이에 대해 약간 다른 형식을 필요로 합니다. 일반적으로이 요소는 알고리즘 정보 (이름, 형식, Oid 또는 유사)와 비밀 키 자료를 포함 합니다. 위의 예제에서 설명자는이 키가 AES-256-CBC-MAC 암호화 + HMACSHA256 유효성 검사를 래핑하는 것으로 지정 합니다.

## <a name="the-encryptedsecret-element"></a>\<encryptedSecret> 요소

[미사용 비밀 암호화를 사용 하는](xref:security/data-protection/implementation/key-encryption-at-rest)경우 비밀 키 자료의 암호화 된 형식을 포함 하는 **&lt; encryptedsecret &gt;** 요소가 있을 수 있습니다. 특성은 `decryptorType` [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)을 구현 하는 형식의 어셈블리로 한정 된 이름입니다. 이 형식은 내부 **&lt; encryptedKey &gt;** 요소를 읽고 암호를 해독 하 여 원래 일반 텍스트를 복구 합니다.

와 마찬가지로 `<descriptor>` 요소의 특정 형식은 `<encryptedSecret>` 사용 중인 미사용 암호화 메커니즘에 따라 달라 집니다. 위의 예제에서 마스터 키는 주석에 따라 Windows DPAPI를 사용 하 여 암호화 됩니다.

## <a name="the-revocation-element"></a>\<revocation> 요소

해지는 키 리포지토리에서 최상위 개체로 존재 합니다. 규칙에 따라 해지는 파일 이름 **해지-{timestamp} .xml** (특정 날짜 이전에 모든 키를 취소) 또는 **해지-{guid} .xml** (특정 키를 취소 하기 위한)을 갖습니다. 각 파일에는 단일 \<revocation> 요소가 포함 됩니다.

개별 키의 해지 파일 내용은 다음과 같습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

이 경우 지정 된 키만 해지 됩니다. 그러나 키 id가 "*" 인 경우에는 아래 예제와 같이 만든 날짜가 지정 된 해지 날짜 이전 인 모든 키가 해지 됩니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<reason>시스템에서 요소를 읽을 수 없습니다. 단지 사용자가 읽을 수 있는 해지 이유를 저장 하는 편리한 장소입니다.
