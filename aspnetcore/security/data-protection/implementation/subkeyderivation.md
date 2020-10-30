---
title: ASP.NET Core에서 하위 키 파생 및 인증 된 암호화
author: rick-anderson
description: ASP.NET Core Data Protection 하위 키 파생 및 인증 된 암호화에 대 한 구현 세부 정보를 알아봅니다.
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: efe8ad2f71feda9cbc1693d362e30eff29cbcd74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060159"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>ASP.NET Core에서 하위 키 파생 및 인증 된 암호화

<a name="data-protection-implementation-subkey-derivation"></a>

키 링의 키 중 대부분은 일종의 엔트로피를 포함 하 고 "CBC 암호화 + HMAC 유효성 검사" 또는 "GCM 암호화 + 유효성 검사"를 나타내는 알고리즘 정보를 포함 합니다. 이러한 경우에는 포함 된 엔트로피를이 키의 마스터 키 자료 (KM)로 참조 하 고, 실제 암호화 작업에 사용할 키를 파생 시키는 키 파생 함수를 수행 합니다.

> [!NOTE]
> 키는 추상적 이며 사용자 지정 구현은 다음과 같이 동작 하지 않을 수 있습니다. 키가 기본 제공 팩터리 중 하나를 사용 하는 대신 자체 구현을 제공 하는 경우 `IAuthenticatedEncryptor` 이 섹션에 설명 된 메커니즘이 더 이상 적용 되지 않습니다.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>추가 인증 된 데이터 및 하위 키 파생

`IAuthenticatedEncryptor`인터페이스는 인증 된 모든 암호화 작업의 핵심 인터페이스 역할을 합니다. 해당 `Encrypt` 메서드는 일반 텍스트와 AAD (additionalAuthenticatedData) 라는 두 개의 버퍼를 사용 합니다. 일반 텍스트 콘텐츠 흐름은에 대 한 호출을 변경 하지 `IDataProtector.Protect` 않지만 AAD는 시스템에 의해 생성 되 고 다음 세 가지 구성 요소로 구성 됩니다.

1. 이 버전의 데이터 보호 시스템을 식별 하는 32 비트 매직 헤더 09 F0 C9 F0입니다.

2. 128 비트 키 id입니다.

3. 이 작업을 수행 하는를 만든 용도 체인에서 형성 된 가변 길이 문자열입니다 `IDataProtector` .

AAD는 세 구성 요소 모두의 튜플에 대해 고유 하기 때문에 모든 암호화 작업에서 KM 자체를 사용 하는 대신 KM에서 새 키를 파생 시키는 데 사용할 수 있습니다. 모든 호출에 대해 `IAuthenticatedEncryptor.Encrypt` 다음과 같은 키 파생 프로세스가 수행 됩니다.

`( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)`

여기서는 다음 매개 변수를 사용 하 여 NIST SP800-108 KDF in 카운터 모드 ( [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), 5.1 참조)를 호출 합니다.

* 키 파생 키 (KDK) = `K_M`

* PRF = HMACSHA512

* 레이블 = additionalAuthenticatedData

* context = contextHeader | | keyModifier

컨텍스트 헤더는 가변 길이 이며 기본적으로 파생 되 고 있는 알고리즘의 손 도장 (thumbprint) 역할을 `K_E` `K_H` 합니다. 키 한정자는에 대 한 각 호출에 임의로 생성 되는 128 비트 문자열이 며, `Encrypt` KDF에 대 한 다른 모든 입력이 일정 한 경우에도이 특정 인증 암호화 작업에 대해 KE 및 KH가 고유한 확률을 보장 하기 위해 사용 됩니다.

CBC 모드 암호화 + HMAC 유효성 검사 작업의 경우 `| K_E |` 는 대칭 블록 암호화 키의 길이 이며은 `| K_H |` HMAC 루틴의 다이제스트 크기입니다. GCM 암호화 + 유효성 검사 작업의 경우 `| K_H | = 0` 입니다.

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC 모드 암호화 + HMAC 유효성 검사

`K_E`위의 메커니즘을 통해 생성 된 후에는 임의의 초기화 벡터를 생성 하 고 대칭 블록 암호화 알고리즘을 실행 하 여 일반 텍스트를 암호화. 그런 다음 초기화 벡터와 암호 텍스트는 키를 사용 하 여 초기화 된 HMAC 루틴을 통해 실행 되어 `K_H` MAC을 생성 합니다. 이 프로세스와 반환 값은 아래에 그래픽으로 표시 됩니다.

![CBC 모드 프로세스 및 반환](subkeyderivation/_static/cbcprocess.png)

`output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))`

> [!NOTE]
> `IDataProtector.Protect`구현은 출력에 [매직 헤더와 키 id를 앞](xref:security/data-protection/implementation/authenticated-encryption-details) 에 추가 하 고 호출자에 게 반환 합니다. 매직 헤더와 키 id는 암시적으로 [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)에 포함 되며 키 한정자가 KDF에 대 한 입력으로 공급 되기 때문에, 최종 반환 된 페이로드의 모든 싱글바이트는 MAC에서 인증 됨을 의미 합니다.

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/카운터 모드 암호화 + 유효성 검사

`K_E`위의 메커니즘을 통해 생성 된 후에는 임의의 96 비트 nonce를 생성 하 고 대칭 블록 암호화 알고리즘을 실행 하 여 일반 텍스트를 암호화 128 비트 인증 태그를 생성 합니다.

![GCM 모드 프로세스 및 반환](subkeyderivation/_static/galoisprocess.png)

`output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag`

> [!NOTE]
> GCM은 기본적으로 AAD의 개념을 지원 하지만 원래 KDF에만 AAD를 공급 하 여 AAD 매개 변수의 GCM에 빈 문자열을 전달 합니다. 이에 대 한 이유는 2 배입니다. 첫째, [민첩성을 지원 하기 위해](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) `K_M` 암호화 키로 직접를 사용 하지 않으려고 합니다. 또한 GCM은 입력에 대해 매우 엄격한 고유성 요구 사항을 적용 합니다. GCM 암호화 루틴이 같은 (키, nonce) 쌍을 사용 하는 두 개 이상의 고유 입력 데이터 집합에 대해 호출 될 확률은 2 ^ 32를 초과 해서는 안 됩니다. 문제를 해결 `K_E` 하는 경우 2 ^-32 제한 afoul를 실행 하기 전에 2 ^ 32 개 이상의 암호화 작업을 수행할 수 없습니다. 이는 많은 수의 작업 처럼 보일 수 있지만 트래픽이 많은 웹 서버는 이러한 키에 대 한 정상 수명 내에서 매우 40억 요청을 수행할 수 있습니다. 2 ^-32 확률 제한을 유지 하기 위해 지정 된에 대 한 사용 가능한 작업 수를 크게 확장 하는 128 비트 키 한정자 및 96 비트 nonce를 계속 사용 합니다 `K_M` . 디자인의 간소화를 위해 CBC와 GCM 작업 간에 KDF 코드 경로를 공유 하 고, AAD는 이미 KDF에서 고려 되므로 GCM 루틴에 전달할 필요가 없습니다.
