---
title: ASP.NET Core의 키 불변성 및 키 설정
author: rick-anderson
description: ASP.NET Core 데이터 보호 키 불변성 Api의 구현 세부 정보에 대해 알아봅니다.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061251"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>ASP.NET Core의 키 불변성 및 키 설정

개체가 백업 저장소에 유지 되 면 해당 표현은 영구적으로 수정 됩니다. 새 데이터는 백업 저장소에 추가할 수 있지만 기존 데이터는 변경할 수 없습니다. 이 동작의 주요 목적은 데이터 손상을 방지 하는 것입니다.

이 동작의 한 가지 결과는 키를 백업 저장소에 기록 하 고 나면 변경할 수 없다는 것입니다. 를 사용 하 여 취소할 수 있지만 생성, 활성화 및 만료 날짜를 변경할 수는 없습니다 `IKeyManager` . 또한 기본 알고리즘 정보, 마스터 키 자료 및 휴지 상태의 암호화 속성도 변경할 수 없습니다.

개발자가 키 지 속성에 영향을 주는 설정을 변경 하는 경우 이러한 변경 내용은에 대 한 명시적 호출 `IKeyManager.CreateNewKey` 또는 데이터 보호 시스템의 자체 [자동 키 생성](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) 동작을 통해 다음에 키를 생성할 때까지 적용 되지 않습니다. 키 지 속성에 영향을 주는 설정은 다음과 같습니다.

* [기본 키 수명](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [휴지 상태의 키 암호화 메커니즘](xref:security/data-protection/implementation/key-encryption-at-rest)

* [키에 포함 된 알고리즘 정보](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

이러한 설정이 다음 자동 키 롤링 시간 보다 먼저 시작 해야 하는 경우에 대 한 명시적 호출을 수행 하 여 `IKeyManager.CreateNewKey` 새 키를 강제로 만들도록 하십시오. 명시적인 활성화 날짜 ({now + 2 일})는 호출에서 변경 내용이 전파 되는 시간을 허용 하는 좋은 방법입니다.

>[!TIP]
> 리포지토리에 접촉 하는 모든 응용 프로그램은 확장명 메서드를 사용 하 여 동일한 설정을 지정 해야 합니다 `IDataProtectionBuilder` . 그렇지 않으면 지속형 키의 속성이 키 생성 루틴을 호출한 특정 응용 프로그램에 종속 됩니다.
