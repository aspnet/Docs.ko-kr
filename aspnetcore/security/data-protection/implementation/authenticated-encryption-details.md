---
title: ASP.NET Core의 인증 된 암호화 세부 정보
author: rick-anderson
description: ASP.NET Core 데이터 보호 인증 된 암호화에 대 한 구현 세부 정보를 알아봅니다.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 7978725534cdd3a5b425851f61b1c7ae3ada88df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051839"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="3052e-103">ASP.NET Core의 인증 된 암호화 세부 정보</span><span class="sxs-lookup"><span data-stu-id="3052e-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="3052e-104">IDataProtector에 대 한 호출은 인증 된 암호화 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="3052e-105">Protect 메서드는 기밀성과 신뢰성을 모두 제공 하며, 루트 IDataProtectionProvider이 특정 IDataProtector 인스턴스를 파생 시키는 데 사용 된 용도 체인에 연결 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="3052e-106">IDataProtector는 byte [] plaintext 매개 변수를 사용 하 고 아래에서 설명 하는 형식에 해당 하는 byte [] 보호 페이로드를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="3052e-107">문자열 일반 텍스트 매개 변수를 사용 하 고 문자열 보호 된 페이로드를 반환 하는 확장 메서드 오버 로드도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="3052e-108">이 API를 사용 하는 경우 보호 된 페이로드 형식은 여전히 아래 구조를 갖지만 [base64url로 인코딩됩니다](https://tools.ietf.org/html/rfc4648#section-5).</span><span class="sxs-lookup"><span data-stu-id="3052e-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="3052e-109">보호 된 페이로드 형식</span><span class="sxs-lookup"><span data-stu-id="3052e-109">Protected payload format</span></span>

<span data-ttu-id="3052e-110">보호 된 페이로드 형식은 다음과 같은 세 가지 주요 구성 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="3052e-111">데이터 보호 시스템의 버전을 식별 하는 32 비트 매직 헤더입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="3052e-112">이 특정 페이로드를 보호 하는 데 사용 되는 키를 식별 하는 128 비트 키 id입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="3052e-113">보호 된 페이로드의 나머지 부분은 [이 키로 캡슐화 된 암호기와 관련이](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)있습니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="3052e-114">아래 예제에서 키는 AES-256-CBC + HMACSHA256 암호기를 나타내며 페이로드는 다음과 같이 추가로 세분화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="3052e-115">128 비트 키 한정자입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="3052e-116">128 비트 초기화 벡터입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="3052e-117">48 바이트의 AES-256-CBC 출력입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="3052e-118">HMACSHA256 인증 태그입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="3052e-119">보호 된 샘플 페이로드는 아래에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-119">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="3052e-120">첫 번째 32 비트 위의 페이로드 형식에서 4 바이트는 버전을 식별 하는 매직 헤더 (09 F0 C9 F0)입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="3052e-121">다음 128 비트 또는 16 바이트는 키 식별자 (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="3052e-122">나머지는 페이로드를 포함 하며 사용 되는 형식에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="3052e-123">지정 된 키로 보호 되는 모든 페이로드는 동일한 20 바이트 (매직 value, key id) 헤더로 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="3052e-124">관리자는이 팩트를 사용 하 여 페이로드가 생성 된 시간을 대략적으로 진단할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="3052e-125">예를 들어 위의 페이로드는 키 {0c819c80-6619-4019-9536-53f8aaffee57}에 해당 합니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="3052e-126">키 리포지토리를 확인 한 후에이 특정 키의 정품 인증 날짜가 2015-01-01이 고 만료 날짜가 2015-03-01 인 경우 해당 창 내에서 페이로드 (변조 되지 않은 경우)가 생성 된 것으로 가정 하 여 어느 쪽에서 나 fudge 비율을 지정 하는 것이 합리적입니다.</span><span class="sxs-lookup"><span data-stu-id="3052e-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
