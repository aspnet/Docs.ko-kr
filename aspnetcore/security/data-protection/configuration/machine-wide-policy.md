---
title: ASP.NET Core의 데이터 보호 컴퓨터 전체 정책 지원
author: rick-anderson
description: ASP.NET Core 데이터 보호를 사용 하는 모든 앱에 대 한 기본 컴퓨터 수준 정책을 설정 하는 지원에 대해 알아봅니다.
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: f6f258e193bf964cb9b4cbd24da2740c5ed89a91
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052996"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>ASP.NET Core의 데이터 보호 컴퓨터 전체 정책 지원

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

Windows에서 실행 하는 경우 데이터 보호 시스템은 ASP.NET Core 데이터 보호를 사용 하는 모든 앱에 대 한 기본 컴퓨터 수준 정책 설정을 제한적으로 지원 합니다. 일반적으로 관리자는 컴퓨터의 모든 앱을 수동으로 업데이트 하지 않고도 사용 되는 알고리즘 또는 키 수명과 같은 기본 설정을 변경할 수 있습니다.

> [!WARNING]
> 시스템 관리자는 기본 정책을 설정할 수 있지만 적용할 수는 없습니다. 앱 개발자는 항상 고유한 선택 중 하나를 사용 하 여 모든 값을 재정의할 수 있습니다. 기본 정책은 개발자가 설정에 대해 명시적 값을 지정 하지 않은 앱에만 영향을 줍니다.

## <a name="setting-default-policy"></a>기본 정책 설정

기본 정책을 설정 하기 위해 관리자는 시스템 레지스트리에서 다음 레지스트리 키를 통해 알려진 값을 설정할 수 있습니다.

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

64 비트 운영 체제를 사용할 때 32 비트 앱의 동작에 영향을 주려면 위의 키에 해당 하는 Wow6432Node를 구성 해야 합니다.

지원 되는 값은 다음과 같습니다.

| 값              | 형식   | 설명 |
| ------------------ | :----: | ----------- |
| EncryptionType     | 문자열 | 데이터 보호에 사용할 알고리즘을 지정 합니다. 이 값은 CNG CBC, CNG-GCM 또는 관리 되는 값 이어야 하며 아래에 자세히 설명 되어 있습니다. |
| DefaultKeyLifetime | DWORD  | 새로 생성 된 키의 수명을 지정 합니다. 값은 일 단위로 지정 되며 >= 7 이어야 합니다. |
| KeyEscrowSinks     | 문자열 | 키 에스크로에 사용 되는 형식을 지정 합니다. 값은 세미콜론으로 구분 된 주요 에스크로 목록으로, 목록의 각 요소는 [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)를 구현 하는 형식의 어셈블리로 한정 된 이름입니다. |

## <a name="encryption-types"></a>암호화 유형

EncryptionType이 CNG 인 경우 시스템은 Windows CNG에서 제공 하는 서비스를 사용 하 여 기밀성 및 HMAC에 대해 CBC의 대칭 블록 암호를 사용 하도록 구성 됩니다 (자세한 내용은 [사용자 지정 WINDOWS CNG 알고리즘 지정](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) 참조). CngCbcAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 다음과 같은 추가 값이 지원 됩니다.

| 값                       | 형식   | 설명 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 문자열 | CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다. 이 알고리즘은 CBC 모드에서 열립니다. |
| EncryptionAlgorithmProvider | 문자열 | 알고리즘 이름 알고리즘을 생성할 수 있는 CNG 공급자 구현의 이름입니다. |
| EncryptionAlgorithmKeySize  | DWORD  | 대칭 블록 암호화 알고리즘에 대해 파생할 키의 길이 (비트)입니다. |
| HashAlgorithm               | 문자열 | CNG에서 인식 하는 해시 알고리즘의 이름입니다. 이 알고리즘은 HMAC 모드에서 열립니다. |
| HashAlgorithmProvider       | 문자열 | HashAlgorithm 알고리즘을 생성할 수 있는 CNG 공급자 구현의 이름입니다. |

EncryptionType이 CNG 인 경우 시스템은 Windows CNG에서 제공 하는 서비스와의 기밀성 및 신뢰성을 위해 Galois/Counter 모드 대칭 블록 암호를 사용 하도록 구성 됩니다 (자세한 내용은 [사용자 지정 WINDOWS CNG 알고리즘 지정](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) 참조). CngGcmAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 다음과 같은 추가 값이 지원 됩니다.

| 값                       | 형식   | 설명 |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | 문자열 | CNG에서 인식 하는 대칭 블록 암호화 알고리즘의 이름입니다. 이 알고리즘은 Galois/카운터 모드에서 열립니다. |
| EncryptionAlgorithmProvider | 문자열 | 알고리즘 이름 알고리즘을 생성할 수 있는 CNG 공급자 구현의 이름입니다. |
| EncryptionAlgorithmKeySize  | DWORD  | 대칭 블록 암호화 알고리즘에 대해 파생할 키의 길이 (비트)입니다. |

EncryptionType가 관리 되는 경우 시스템은 기밀성 및 KeyedHashAlgorithm에 대 한 관리 되는 System.security.cryptography.symmetricalgorithm를 사용 하도록 구성 됩니다 (자세한 내용은 [사용자 지정 관리 알고리즘 지정](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) 참조). ManagedAuthenticatedEncryptionSettings 형식의 속성에 해당 하는 다음과 같은 추가 값이 지원 됩니다.

| 값                      | 형식   | 설명 |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | 문자열 | System.security.cryptography.symmetricalgorithm을 구현 하는 형식의 정규화 된 어셈블리 이름입니다. |
| EncryptionAlgorithmKeySize | DWORD  | 대칭 암호화 알고리즘에 대해 파생할 키의 길이 (비트)입니다. |
| ValidationAlgorithmType    | 문자열 | KeyedHashAlgorithm을 구현 하는 형식의 정규화 된 어셈블리 이름입니다. |

EncryptionType에 null이 아닌 다른 값이 있거나 비어 있는 경우 데이터 보호 시스템은 시작할 때 예외를 throw 합니다.

> [!WARNING]
> 형식 이름 (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks)을 포함 하는 기본 정책 설정을 구성 하는 경우 앱에서 해당 형식을 사용할 수 있어야 합니다. 즉, 데스크톱 CLR에서 실행 되는 앱의 경우 이러한 형식을 포함 하는 어셈블리는 GAC (전역 어셈블리 캐시)에 있어야 합니다. .NET Core에서 실행 되는 ASP.NET Core 앱의 경우 이러한 형식을 포함 하는 패키지를 설치 해야 합니다.
