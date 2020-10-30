---
title: ASP.NET Core의 인증 된 암호화 세부 정보
author: rick-anderson
description: ASP.NET Core 데이터 보호 인증 된 암호화에 대 한 구현 세부 정보를 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 7978725534cdd3a5b425851f61b1c7ae3ada88df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051839"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>ASP.NET Core의 인증 된 암호화 세부 정보

<a name="data-protection-implementation-authenticated-encryption-details"></a>

IDataProtector에 대 한 호출은 인증 된 암호화 작업입니다. Protect 메서드는 기밀성과 신뢰성을 모두 제공 하며, 루트 IDataProtectionProvider이 특정 IDataProtector 인스턴스를 파생 시키는 데 사용 된 용도 체인에 연결 되어 있습니다.

IDataProtector는 byte [] plaintext 매개 변수를 사용 하 고 아래에서 설명 하는 형식에 해당 하는 byte [] 보호 페이로드를 생성 합니다. 문자열 일반 텍스트 매개 변수를 사용 하 고 문자열 보호 된 페이로드를 반환 하는 확장 메서드 오버 로드도 있습니다. 이 API를 사용 하는 경우 보호 된 페이로드 형식은 여전히 아래 구조를 갖지만 [base64url로 인코딩됩니다](https://tools.ietf.org/html/rfc4648#section-5).

## <a name="protected-payload-format"></a>보호 된 페이로드 형식

보호 된 페이로드 형식은 다음과 같은 세 가지 주요 구성 요소로 구성 됩니다.

* 데이터 보호 시스템의 버전을 식별 하는 32 비트 매직 헤더입니다.

* 이 특정 페이로드를 보호 하는 데 사용 되는 키를 식별 하는 128 비트 키 id입니다.

* 보호 된 페이로드의 나머지 부분은 [이 키로 캡슐화 된 암호기와 관련이](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)있습니다. 아래 예제에서 키는 AES-256-CBC + HMACSHA256 암호기를 나타내며 페이로드는 다음과 같이 추가로 세분화 됩니다.
  * 128 비트 키 한정자입니다.
  * 128 비트 초기화 벡터입니다.
  * 48 바이트의 AES-256-CBC 출력입니다.
  * HMACSHA256 인증 태그입니다.

보호 된 샘플 페이로드는 아래에 나와 있습니다.

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

첫 번째 32 비트 위의 페이로드 형식에서 4 바이트는 버전을 식별 하는 매직 헤더 (09 F0 C9 F0)입니다.

다음 128 비트 또는 16 바이트는 키 식별자 (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)입니다.

나머지는 페이로드를 포함 하며 사용 되는 형식에만 적용 됩니다.

> [!WARNING]
> 지정 된 키로 보호 되는 모든 페이로드는 동일한 20 바이트 (매직 value, key id) 헤더로 시작 합니다. 관리자는이 팩트를 사용 하 여 페이로드가 생성 된 시간을 대략적으로 진단할 수 있습니다. 예를 들어 위의 페이로드는 키 {0c819c80-6619-4019-9536-53f8aaffee57}에 해당 합니다. 키 리포지토리를 확인 한 후에이 특정 키의 정품 인증 날짜가 2015-01-01이 고 만료 날짜가 2015-03-01 인 경우 해당 창 내에서 페이로드 (변조 되지 않은 경우)가 생성 된 것으로 가정 하 여 어느 쪽에서 나 fudge 비율을 지정 하는 것이 합리적입니다.
