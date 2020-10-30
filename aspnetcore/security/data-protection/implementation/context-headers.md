---
title: ASP.NET Core의 컨텍스트 헤더
author: rick-anderson
description: ASP.NET Core 데이터 보호 컨텍스트 헤더에 대 한 구현 세부 정보를 알아봅니다.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051722"
---
# <a name="context-headers-in-aspnet-core"></a>ASP.NET Core의 컨텍스트 헤더

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>배경 및 이론

데이터 보호 시스템에서 "키"는 인증 된 암호화 서비스를 제공할 수 있는 개체를 의미 합니다. 각 키는 고유 id (GUID)로 식별 되며 it 알고리즘 정보와 entropic 자료를 사용 하 여 전달 됩니다. 각 키가 고유한 엔트로피를 제공 하지만 시스템에서이를 적용할 수는 없지만 키 링에서 기존 키의 알고리즘 정보를 수정 하 여 키 링을 수동으로 변경할 수 있는 개발자를 고려해 야 합니다. 이러한 경우에는 보안 요구 사항을 충족 하기 위해 데이터 보호 시스템에 [암호화 민첩성](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)이라는 개념이 있으므로 여러 암호화 알고리즘에서 단일 entropic 값을 안전 하 게 사용할 수 있습니다.

암호화 민첩성을 지 원하는 대부분의 시스템은 페이로드 내에서 알고리즘에 대 한 일부 식별 정보를 포함 하 여이 작업을 수행 합니다. 일반적으로 알고리즘의 OID는이에 적합 한 후보입니다. 그러나 한 가지 문제는 동일한 알고리즘을 지정 하는 여러 가지 방법이 있습니다. 즉, "AES" (CNG) 및 관리 되는 Aes, AesManaged, AesCryptoServiceProvider, AesCng 및 RijndaelManaged (지정 된 특정 매개 변수) 클래스는 모두 실제로 동일한 작업을 수행 하며, 이러한 모든 항목의 매핑을 올바른 OID로 유지 해야 합니다. 개발자가 사용자 지정 알고리즘 (또는 다른 AES! 구현)을 제공 하려는 경우 해당 OID를 알려주세요. 이 추가 등록 단계를 수행 하면 시스템 구성이 특히 어려워집니다.

이전 단계에서 문제에 대해 잘못 된 방향으로 접근 한다고 결정 했습니다. OID를 통해 알고리즘이 무엇 인지 알 수 있지만 실제로는 이러한 사실을 걱정 하지 않습니다. 서로 다른 두 알고리즘에서 단일 entropic 값을 안전 하 게 사용 해야 하는 경우 알고리즘이 실제로 무엇 인지 알 필요가 없습니다. 실제로 중요 한 것은 어떻게 동작 합니다. 모든 적절 한 대칭 블록 암호화 알고리즘은 강력한 의사 (pseudo) 순열 (PRP) 이기도 합니다. 입력 (키, 체인 모드, IV, 일반 텍스트)을 수정 하 고, 암호 텍스트 출력은 동일한 입력에 지정 된 다른 대칭 블록 암호화 알고리즘과 크게 다르지 않습니다. 마찬가지로, 모든 적절 한 키 지정 해시 함수는 PRF (강력한 의사 난수 함수) 이기도 하며 고정 입력 집합을 지정 하는 경우 해당 출력은 다른 키 지정 해시 함수와 대다수 수 있습니다.

이 강력한 PRPs 및 Prps 라는 개념을 사용 하 여 컨텍스트 헤더를 작성 합니다. 이 컨텍스트 헤더는 기본적으로 지정 된 작업에 사용 되는 알고리즘에 대 한 안정적인 손 도장 (thumbprint) 역할을 하며, 데이터 보호 시스템에 필요한 암호화 민첩성을 제공 합니다. 이 헤더는 재현할 수 있으며 나중에 [하위 키 파생 프로세스](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)의 일부로 사용 됩니다. 기본 알고리즘의 작업 모드에 따라 컨텍스트 헤더를 작성 하는 두 가지 다른 방법이 있습니다.

## <a name="cbc-mode-encryption--hmac-authentication"></a>CBC 모드 암호화 + HMAC 인증

<a name="data-protection-implementation-context-headers-cbc-components"></a>

컨텍스트 헤더는 다음 구성 요소로 구성 됩니다.

* [16 비트] 값 00 00는 "CBC 암호화 + HMAC 인증"을 의미 합니다.

* [32 비트] 대칭 블록 암호화 알고리즘의 키 길이 (바이트, 빅 endian)입니다.

* [32 비트] 대칭 블록 암호화 알고리즘의 블록 크기 (바이트, 빅 endian)입니다.

* [32 비트] HMAC 알고리즘의 키 길이 (바이트, 빅 endian)입니다. 현재 키 크기는 항상 다이제스트 크기와 일치 합니다.

* [32 비트] HMAC 알고리즘의 다이제스트 크기 (바이트, 빅 endian)입니다.

* `EncCBC(K_E, IV, "")`는 빈 문자열 입력이 제공 된 대칭 블록 암호화 알고리즘의 출력입니다. 여기서 IV는 모두 0 벡터입니다. 의 구성은 `K_E` 아래에 설명 되어 있습니다.

* `MAC(K_H, "")`-빈 문자열 입력이 제공 된 HMAC 알고리즘의 출력입니다. 의 구성은 `K_H` 아래에 설명 되어 있습니다.

이상적으로 및에 대 한 모든 0 벡터를 전달할 수 있습니다 `K_E` `K_H` . 그러나 기본 알고리즘에서 작업을 수행 하기 전에 weak 키의 존재 여부를 확인 하는 상황을 방지 하는 것이 좋습니다 .이 경우에는 0이 아닌 벡터와 같은 단순 하거나 반복 가능한 패턴을 사용 하지 않습니다.

대신, KDF 키, 레이블, 컨텍스트 및 HMACSHA512를 기본 PRF로 사용 하 여 NIST SP800-108 ( [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 5.1 참조)을 사용 합니다. `| K_E | + | K_H |`바이트의 출력을 파생 시킨 다음 결과를 `K_E` 및 자체로 분해 `K_H` 합니다. 수학적으로 다음과 같이 표시 됩니다.

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a>예: AES-192-CBC + HMACSHA256

예를 들어 대칭 블록 암호화 알고리즘이 AES-192-CBC이 고 유효성 검사 알고리즘이 HMACSHA256 경우를 고려해 보세요. 시스템은 다음 단계를 사용 하 여 컨텍스트 헤더를 생성 합니다.

첫 번째는 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` 지정 된 알고리즘에 따라, 여기서 및입니다 `| K_H | = 256 bits` . 그러면 `K_E = 5BB6..21DD` `K_H = A04A..00A9` 아래 예제에서 및가 발생 합니다.

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

다음으로, `Enc_CBC (K_E, IV, "")` 지정 된 및 위와 같이 AES-192-CBC를 계산 `IV = 0*` `K_E` 합니다.

`result := F474B1872B3B53E4721DE19C0841DB6F`

다음으로, `MAC(K_H, "")` 위와 같이 제공 된 HMACSHA256에 대해 계산 `K_H` 합니다.

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

이렇게 하면 아래 전체 컨텍스트 헤더가 생성 됩니다.

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

이 컨텍스트 헤더는 인증 된 암호화 알고리즘 쌍 (AES-192-CBC encryption + HMACSHA256 validation)의 지문입니다. [위에서](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) 설명한 구성 요소는 다음과 같습니다.

* 표식 `(00 00)`

* 블록 암호화 키 길이입니다. `(00 00 00 18)`

* 블록 암호화 블록 크기입니다. `(00 00 00 10)`

* HMAC 키 길이입니다. `(00 00 00 20)`

* HMAC 다이제스트 크기 `(00 00 00 20)`

* 블록 암호화 PRP 출력 `(F4 74 - DB 6F)` 및

* HMAC PRF 출력 `(D4 79 - end)` 입니다.

> [!NOTE]
> Windows CNG 또는 관리 되는 System.security.cryptography.symmetricalgorithm 및 KeyedHashAlgorithm 유형에 의해 알고리즘 구현이 제공 되는지 여부에 관계 없이 CBC 모드 암호화 + HMAC 인증 컨텍스트 헤더는 동일한 방식으로 빌드됩니다. 이를 통해 다른 운영 체제에서 실행 되는 응용 프로그램은 Os 마다 알고리즘의 구현이 서로 다른 경우에도 동일한 컨텍스트 헤더를 안정적으로 생성할 수 있습니다. 실제로 KeyedHashAlgorithm는 적절 한 HMAC가 아니어도 됩니다. 키가 지정 된 해시 알고리즘 형식일 수 있습니다.

### <a name="example-3des-192-cbc--hmacsha1"></a>예: 3DES-192-CBC + HMACSHA1

첫 번째는 `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` 지정 된 알고리즘에 따라, 여기서 및입니다 `| K_H | = 160 bits` . 그러면 `K_E = A219..E2BB` `K_H = DC4A..B464` 아래 예제에서 및가 발생 합니다.

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

그런 다음, `Enc_CBC (K_E, IV, "")` 지정 된 3des-192-CBC `IV = 0*` 를 `K_E` 위와 같이 계산 합니다.

`result := ABB100F81E53E10E`

다음으로, `MAC(K_H, "")` 위와 같이 제공 된 HMACSHA1에 대해 계산 `K_H` 합니다.

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

이렇게 하면 아래와 같이 인증 된 암호화 알고리즘 쌍 (3DES-192-CBC encryption + HMACSHA1 validation)의 지문이 되는 전체 컨텍스트 헤더가 생성 됩니다.

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

구성 요소는 다음과 같이 구분 됩니다.

* 표식 `(00 00)`

* 블록 암호화 키 길이입니다. `(00 00 00 18)`

* 블록 암호화 블록 크기입니다. `(00 00 00 08)`

* HMAC 키 길이입니다. `(00 00 00 14)`

* HMAC 다이제스트 크기 `(00 00 00 14)`

* 블록 암호화 PRP 출력 `(AB B1 - E1 0E)` 및

* HMAC PRF 출력 `(76 EB - end)` 입니다.

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/카운터 모드 암호화 + 인증

컨텍스트 헤더는 다음 구성 요소로 구성 됩니다.

* [16 비트] 값 00 01는 "GCM 암호화 + 인증"을 의미 합니다.

* [32 비트] 대칭 블록 암호화 알고리즘의 키 길이 (바이트, 빅 endian)입니다.

* [32 비트] 인증 된 암호화 작업 중에 사용 되는 nonce 크기 (바이트, 빅 endian)입니다. (시스템의 경우 nonce 크기 = 96 비트에서이 문제가 해결 됩니다.)

* [32 비트] 대칭 블록 암호화 알고리즘의 블록 크기 (바이트, 빅 endian)입니다. GCM의 경우 블록 크기 = 128 비트에서 수정 됩니다.

* [32 비트] 인증 된 암호화 함수에 의해 생성 된 인증 태그 크기 (바이트, 빅 endian)입니다. 시스템의 경우이는 태그 크기 = 128 비트에서 수정 되었습니다.

* [128 비트] `Enc_GCM (K_E, nonce, "")`빈 문자열 입력이 지정 되 고 nonce가 96 비트의 0 비트 벡터로 인의 태그입니다.

`K_E` 는 CBC 암호화 + HMAC 인증 시나리오와 동일한 메커니즘을 사용 하 여 파생 됩니다. 그러나 `K_H` 여기에는 재생이 없으므로 기본적으로가 있고 `| K_H | = 0` 알고리즘은 아래 형태로 축소 됩니다.

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a>예: AES-256-GCM

먼저, `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 여기서을 사용 `| K_E | = 256 bits` 합니다.

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

그런 다음, `Enc_GCM (K_E, nonce, "")` 제공 된 AES-256-GCM의 인증 태그를 `nonce = 096` `K_E` 위와 같이 계산 합니다.

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

이렇게 하면 아래 전체 컨텍스트 헤더가 생성 됩니다.

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

구성 요소는 다음과 같이 구분 됩니다.

* 표식 `(00 01)`

* 블록 암호화 키 길이입니다. `(00 00 00 20)`

* nonce 크기 `(00 00 00 0C)`

* 블록 암호화 블록 크기입니다. `(00 00 00 10)`

* 인증 태그 크기 `(00 00 00 10)` 및

* 블록 암호화를 실행 하는 인증 태그 `(E7 DC - end)` 입니다.
