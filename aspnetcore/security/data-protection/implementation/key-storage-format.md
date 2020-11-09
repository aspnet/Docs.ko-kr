---
title: ASP.NET Core의 키 저장소 형식
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 저장소 형식에 대 한 구현 세부 정보를 알아봅니다.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060146"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="c5b8a-103">ASP.NET Core의 키 저장소 형식</span><span class="sxs-lookup"><span data-stu-id="c5b8a-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="c5b8a-104">개체는 미사용 위치에 XML 표현으로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="c5b8a-105">키 저장소의 기본 디렉터리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="c5b8a-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="c5b8a-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="c5b8a-107">macOS/Linux: *$HOME/.aspnet/dataprotection-keys*</span><span class="sxs-lookup"><span data-stu-id="c5b8a-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="c5b8a-108">\<key> 요소</span><span class="sxs-lookup"><span data-stu-id="c5b8a-108">The \<key> element</span></span>

<span data-ttu-id="c5b8a-109">키가 키 리포지토리에서 최상위 개체로 존재 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="c5b8a-110">규칙에 따라 키에는 파일 이름 **-{guid} .xml** 이 있습니다. 여기서 {guid}는 키의 id입니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-110">By convention keys have the filename **key-{guid}.xml** , where {guid} is the id of the key.</span></span> <span data-ttu-id="c5b8a-111">이러한 각 파일에는 단일 키가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-111">Each such file contains a single key.</span></span> <span data-ttu-id="c5b8a-112">파일 형식은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="c5b8a-113">\<key>요소는 다음 특성 및 자식 요소를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="c5b8a-114">키 id입니다. 이 값은 권한 있는 것으로 간주 됩니다. filename은 사람이 읽을 수 있는 nicety 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="c5b8a-115">\<key>현재 1에서 고정 된 요소의 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="c5b8a-116">키의 생성, 활성화 및 만료 날짜입니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="c5b8a-117">\<descriptor>이 키에 포함 된 인증 된 암호화 구현에 대 한 정보를 포함 하는 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="c5b8a-118">위의 예제에서 키의 id는 {80732141-ec8f-af9c-c4d2d1ff8901}이 고 3 월 2015 19 일에 만들어지고 활성화 되었으며 90 일의 수명이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="c5b8a-119">(이 예제에서와 같이 활성화 날짜가 만든 날짜 보다 약간 약간 낮을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="c5b8a-120">이는 Api가 작동 하는 방식에 사소한 하 고 실제로는 바람직하지 않기 때문입니다.)</span><span class="sxs-lookup"><span data-stu-id="c5b8a-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="c5b8a-121">\<descriptor> 요소</span><span class="sxs-lookup"><span data-stu-id="c5b8a-121">The \<descriptor> element</span></span>

<span data-ttu-id="c5b8a-122">외부 \<descriptor> 요소는 IAuthenticatedEncryptorDescriptorDeserializer를 구현 하는 형식의 어셈블리로 한정 된 이름인 deserializerType 특성을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="c5b8a-123">이 형식은 내부 요소를 읽고 내에 \<descriptor> 포함 된 정보를 구문 분석 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="c5b8a-124">요소의 특정 형식은 \<descriptor> 키로 캡슐화 된 인증 된 암호기 구현에 따라 달라 지 며 각 역직렬 변환기 형식은이에 대해 약간 다른 형식을 필요로 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="c5b8a-125">일반적으로이 요소는 알고리즘 정보 (이름, 형식, Oid 또는 유사)와 비밀 키 자료를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="c5b8a-126">위의 예제에서 설명자는이 키가 AES-256-CBC-MAC 암호화 + HMACSHA256 유효성 검사를 래핑하는 것으로 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="c5b8a-127">\<encryptedSecret> 요소</span><span class="sxs-lookup"><span data-stu-id="c5b8a-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="c5b8a-128">[미사용 비밀 암호화를 사용 하는](xref:security/data-protection/implementation/key-encryption-at-rest)경우 비밀 키 자료의 암호화 된 형식을 포함 하는 **&lt; encryptedsecret &gt;** 요소가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="c5b8a-129">특성은 `decryptorType` [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)을 구현 하는 형식의 어셈블리로 한정 된 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="c5b8a-130">이 형식은 내부 **&lt; encryptedKey &gt;** 요소를 읽고 암호를 해독 하 여 원래 일반 텍스트를 복구 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="c5b8a-131">와 마찬가지로 `<descriptor>` 요소의 특정 형식은 `<encryptedSecret>` 사용 중인 미사용 암호화 메커니즘에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="c5b8a-132">위의 예제에서 마스터 키는 주석에 따라 Windows DPAPI를 사용 하 여 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="c5b8a-133">\<revocation> 요소</span><span class="sxs-lookup"><span data-stu-id="c5b8a-133">The \<revocation> element</span></span>

<span data-ttu-id="c5b8a-134">해지는 키 리포지토리에서 최상위 개체로 존재 합니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="c5b8a-135">규칙에 따라 해지는 파일 이름 **해지-{timestamp} .xml** (특정 날짜 이전에 모든 키를 취소) 또는 **해지-{guid} .xml** (특정 키를 취소 하기 위한)을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="c5b8a-136">각 파일에는 단일 \<revocation> 요소가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="c5b8a-137">개별 키의 해지 파일 내용은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="c5b8a-138">이 경우 지정 된 키만 해지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="c5b8a-139">그러나 키 id가 "\*" 인 경우에는 아래 예제와 같이 만든 날짜가 지정 된 해지 날짜 이전 인 모든 키가 해지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="c5b8a-140">\<reason>시스템에서 요소를 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="c5b8a-141">단지 사용자가 읽을 수 있는 해지 이유를 저장 하는 편리한 장소입니다.</span><span class="sxs-lookup"><span data-stu-id="c5b8a-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
