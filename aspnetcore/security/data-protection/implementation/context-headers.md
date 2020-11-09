---
title: ASP.NET Core의 컨텍스트 헤더
author: rick-anderson
description: ASP.NET Core 데이터 보호 컨텍스트 헤더에 대 한 구현 세부 정보를 알아봅니다.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051722"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="c88c4-103">ASP.NET Core의 컨텍스트 헤더</span><span class="sxs-lookup"><span data-stu-id="c88c4-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="c88c4-104">배경 및 이론</span><span class="sxs-lookup"><span data-stu-id="c88c4-104">Background and theory</span></span>

<span data-ttu-id="c88c4-105">데이터 보호 시스템에서 "키"는 인증 된 암호화 서비스를 제공할 수 있는 개체를 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="c88c4-106">각 키는 고유 id (GUID)로 식별 되며 it 알고리즘 정보와 entropic 자료를 사용 하 여 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="c88c4-107">각 키가 고유한 엔트로피를 제공 하지만 시스템에서이를 적용할 수는 없지만 키 링에서 기존 키의 알고리즘 정보를 수정 하 여 키 링을 수동으로 변경할 수 있는 개발자를 고려해 야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="c88c4-108">이러한 경우에는 보안 요구 사항을 충족 하기 위해 데이터 보호 시스템에 [암호화 민첩성](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)이라는 개념이 있으므로 여러 암호화 알고리즘에서 단일 entropic 값을 안전 하 게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="c88c4-109">암호화 민첩성을 지 원하는 대부분의 시스템은 페이로드 내에서 알고리즘에 대 한 일부 식별 정보를 포함 하 여이 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="c88c4-110">일반적으로 알고리즘의 OID는이에 적합 한 후보입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="c88c4-111">그러나 한 가지 문제는 동일한 알고리즘을 지정 하는 여러 가지 방법이 있습니다. 즉, "AES" (CNG) 및 관리 되는 Aes, AesManaged, AesCryptoServiceProvider, AesCng 및 RijndaelManaged (지정 된 특정 매개 변수) 클래스는 모두 실제로 동일한 작업을 수행 하며, 이러한 모든 항목의 매핑을 올바른 OID로 유지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="c88c4-112">개발자가 사용자 지정 알고리즘 (또는 다른 AES! 구현)을 제공 하려는 경우 해당 OID를 알려주세요.</span><span class="sxs-lookup"><span data-stu-id="c88c4-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="c88c4-113">이 추가 등록 단계를 수행 하면 시스템 구성이 특히 어려워집니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="c88c4-114">이전 단계에서 문제에 대해 잘못 된 방향으로 접근 한다고 결정 했습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="c88c4-115">OID를 통해 알고리즘이 무엇 인지 알 수 있지만 실제로는 이러한 사실을 걱정 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="c88c4-116">서로 다른 두 알고리즘에서 단일 entropic 값을 안전 하 게 사용 해야 하는 경우 알고리즘이 실제로 무엇 인지 알 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="c88c4-117">실제로 중요 한 것은 어떻게 동작 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="c88c4-118">모든 적절 한 대칭 블록 암호화 알고리즘은 강력한 의사 (pseudo) 순열 (PRP) 이기도 합니다. 입력 (키, 체인 모드, IV, 일반 텍스트)을 수정 하 고, 암호 텍스트 출력은 동일한 입력에 지정 된 다른 대칭 블록 암호화 알고리즘과 크게 다르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="c88c4-119">마찬가지로, 모든 적절 한 키 지정 해시 함수는 PRF (강력한 의사 난수 함수) 이기도 하며 고정 입력 집합을 지정 하는 경우 해당 출력은 다른 키 지정 해시 함수와 대다수 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="c88c4-120">이 강력한 PRPs 및 Prps 라는 개념을 사용 하 여 컨텍스트 헤더를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="c88c4-121">이 컨텍스트 헤더는 기본적으로 지정 된 작업에 사용 되는 알고리즘에 대 한 안정적인 손 도장 (thumbprint) 역할을 하며, 데이터 보호 시스템에 필요한 암호화 민첩성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="c88c4-122">이 헤더는 재현할 수 있으며 나중에 [하위 키 파생 프로세스](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)의 일부로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="c88c4-123">기본 알고리즘의 작업 모드에 따라 컨텍스트 헤더를 작성 하는 두 가지 다른 방법이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="c88c4-124">CBC 모드 암호화 + HMAC 인증</span><span class="sxs-lookup"><span data-stu-id="c88c4-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="c88c4-125">컨텍스트 헤더는 다음 구성 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="c88c4-126">[16 비트] 값 00 00는 "CBC 암호화 + HMAC 인증"을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="c88c4-127">[32 비트] 대칭 블록 암호화 알고리즘의 키 길이 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c88c4-128">[32 비트] 대칭 블록 암호화 알고리즘의 블록 크기 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c88c4-129">[32 비트] HMAC 알고리즘의 키 길이 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="c88c4-130">현재 키 크기는 항상 다이제스트 크기와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="c88c4-131">[32 비트] HMAC 알고리즘의 다이제스트 크기 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="c88c4-132">`EncCBC(K_E, IV, "")`는 빈 문자열 입력이 제공 된 대칭 블록 암호화 알고리즘의 출력입니다. 여기서 IV는 모두 0 벡터입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="c88c4-133">의 구성은 `K_E` 아래에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="c88c4-134">`MAC(K_H, "")`-빈 문자열 입력이 제공 된 HMAC 알고리즘의 출력입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="c88c4-135">의 구성은 `K_H` 아래에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="c88c4-136">이상적으로 및에 대 한 모든 0 벡터를 전달할 수 있습니다 `K_E` `K_H` .</span><span class="sxs-lookup"><span data-stu-id="c88c4-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="c88c4-137">그러나 기본 알고리즘에서 작업을 수행 하기 전에 weak 키의 존재 여부를 확인 하는 상황을 방지 하는 것이 좋습니다 .이 경우에는 0이 아닌 벡터와 같은 단순 하거나 반복 가능한 패턴을 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="c88c4-138">대신, KDF 키, 레이블, 컨텍스트 및 HMACSHA512를 기본 PRF로 사용 하 여 NIST SP800-108 ( [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 5.1 참조)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="c88c4-139">`| K_E | + | K_H |`바이트의 출력을 파생 시킨 다음 결과를 `K_E` 및 자체로 분해 `K_H` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="c88c4-140">수학적으로 다음과 같이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="c88c4-141">예: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="c88c4-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="c88c4-142">예를 들어 대칭 블록 암호화 알고리즘이 AES-192-CBC이 고 유효성 검사 알고리즘이 HMACSHA256 경우를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="c88c4-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="c88c4-143">시스템은 다음 단계를 사용 하 여 컨텍스트 헤더를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="c88c4-144">첫 번째는 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` 지정 된 알고리즘에 따라, 여기서 및입니다 `| K_H | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="c88c4-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="c88c4-145">그러면 `K_E = 5BB6..21DD` `K_H = A04A..00A9` 아래 예제에서 및가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="c88c4-146">다음으로, `Enc_CBC (K_E, IV, "")` 지정 된 및 위와 같이 AES-192-CBC를 계산 `IV = 0*` `K_E` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="c88c4-147">다음으로, `MAC(K_H, "")` 위와 같이 제공 된 HMACSHA256에 대해 계산 `K_H` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="c88c4-148">이렇게 하면 아래 전체 컨텍스트 헤더가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="c88c4-149">이 컨텍스트 헤더는 인증 된 암호화 알고리즘 쌍 (AES-192-CBC encryption + HMACSHA256 validation)의 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="c88c4-150">[위에서](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) 설명한 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="c88c4-151">표식 `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="c88c4-152">블록 암호화 키 길이입니다. `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="c88c4-153">블록 암호화 블록 크기입니다. `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="c88c4-154">HMAC 키 길이입니다. `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="c88c4-155">HMAC 다이제스트 크기 `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="c88c4-156">블록 암호화 PRP 출력 `(F4 74 - DB 6F)` 및</span><span class="sxs-lookup"><span data-stu-id="c88c4-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="c88c4-157">HMAC PRF 출력 `(D4 79 - end)` 입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="c88c4-158">Windows CNG 또는 관리 되는 System.security.cryptography.symmetricalgorithm 및 KeyedHashAlgorithm 유형에 의해 알고리즘 구현이 제공 되는지 여부에 관계 없이 CBC 모드 암호화 + HMAC 인증 컨텍스트 헤더는 동일한 방식으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="c88c4-159">이를 통해 다른 운영 체제에서 실행 되는 응용 프로그램은 Os 마다 알고리즘의 구현이 서로 다른 경우에도 동일한 컨텍스트 헤더를 안정적으로 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="c88c4-160">실제로 KeyedHashAlgorithm는 적절 한 HMAC가 아니어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="c88c4-161">키가 지정 된 해시 알고리즘 형식일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="c88c4-162">예: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="c88c4-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="c88c4-163">첫 번째는 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` 지정 된 알고리즘에 따라, 여기서 및입니다 `| K_H | = 160 bits` .</span><span class="sxs-lookup"><span data-stu-id="c88c4-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="c88c4-164">그러면 `K_E = A219..E2BB` `K_H = DC4A..B464` 아래 예제에서 및가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="c88c4-165">그런 다음, `Enc_CBC (K_E, IV, "")` 지정 된 3des-192-CBC `IV = 0*` 를 `K_E` 위와 같이 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="c88c4-166">다음으로, `MAC(K_H, "")` 위와 같이 제공 된 HMACSHA1에 대해 계산 `K_H` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="c88c4-167">이렇게 하면 아래와 같이 인증 된 암호화 알고리즘 쌍 (3DES-192-CBC encryption + HMACSHA1 validation)의 지문이 되는 전체 컨텍스트 헤더가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="c88c4-168">구성 요소는 다음과 같이 구분 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-168">The components break down as follows:</span></span>

* <span data-ttu-id="c88c4-169">표식 `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="c88c4-170">블록 암호화 키 길이입니다. `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="c88c4-171">블록 암호화 블록 크기입니다. `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="c88c4-172">HMAC 키 길이입니다. `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="c88c4-173">HMAC 다이제스트 크기 `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="c88c4-174">블록 암호화 PRP 출력 `(AB B1 - E1 0E)` 및</span><span class="sxs-lookup"><span data-stu-id="c88c4-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="c88c4-175">HMAC PRF 출력 `(76 EB - end)` 입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="c88c4-176">Galois/카운터 모드 암호화 + 인증</span><span class="sxs-lookup"><span data-stu-id="c88c4-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="c88c4-177">컨텍스트 헤더는 다음 구성 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="c88c4-178">[16 비트] 값 00 01는 "GCM 암호화 + 인증"을 의미 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="c88c4-179">[32 비트] 대칭 블록 암호화 알고리즘의 키 길이 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c88c4-180">[32 비트] 인증 된 암호화 작업 중에 사용 되는 nonce 크기 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="c88c4-181">(시스템의 경우 nonce 크기 = 96 비트에서이 문제가 해결 됩니다.)</span><span class="sxs-lookup"><span data-stu-id="c88c4-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="c88c4-182">[32 비트] 대칭 블록 암호화 알고리즘의 블록 크기 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="c88c4-183">GCM의 경우 블록 크기 = 128 비트에서 수정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="c88c4-184">[32 비트] 인증 된 암호화 함수에 의해 생성 된 인증 태그 크기 (바이트, 빅 endian)입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="c88c4-185">시스템의 경우이는 태그 크기 = 128 비트에서 수정 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="c88c4-186">[128 비트] `Enc_GCM (K_E, nonce, "")`빈 문자열 입력이 지정 되 고 nonce가 96 비트의 0 비트 벡터로 인의 태그입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="c88c4-187">`K_E` 는 CBC 암호화 + HMAC 인증 시나리오와 동일한 메커니즘을 사용 하 여 파생 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="c88c4-188">그러나 `K_H` 여기에는 재생이 없으므로 기본적으로가 있고 `| K_H | = 0` 알고리즘은 아래 형태로 축소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="c88c4-189">예: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="c88c4-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="c88c4-190">먼저, `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 여기서을 사용 `| K_E | = 256 bits` 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="c88c4-191">그런 다음, `Enc_GCM (K_E, nonce, "")` 제공 된 AES-256-GCM의 인증 태그를 `nonce = 096` `K_E` 위와 같이 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="c88c4-192">이렇게 하면 아래 전체 컨텍스트 헤더가 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="c88c4-193">구성 요소는 다음과 같이 구분 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-193">The components break down as follows:</span></span>

* <span data-ttu-id="c88c4-194">표식 `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="c88c4-195">블록 암호화 키 길이입니다. `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="c88c4-196">nonce 크기 `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="c88c4-197">블록 암호화 블록 크기입니다. `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="c88c4-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="c88c4-198">인증 태그 크기 `(00 00 00 10)` 및</span><span class="sxs-lookup"><span data-stu-id="c88c4-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="c88c4-199">블록 암호화를 실행 하는 인증 태그 `(E7 DC - end)` 입니다.</span><span class="sxs-lookup"><span data-stu-id="c88c4-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
