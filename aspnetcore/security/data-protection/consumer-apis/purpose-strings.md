---
title: ASP.NET Core의 용도 문자열
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api에서 용도 문자열이 사용 되는 방법에 대해 알아봅니다.
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060809"
---
# <a name="purpose-strings-in-aspnet-core"></a>ASP.NET Core의 용도 문자열

<a name="data-protection-consumer-apis-purposes"></a>

을 사용 하는 구성 요소는 `IDataProtectionProvider` 메서드에 고유한 *용도* 매개 변수를 전달 해야 합니다 `CreateProtector` . 목적 *매개 변수* 는 루트 암호화 키가 동일한 경우에도 암호화 소비자 간의 격리를 제공 하므로 데이터 보호 시스템의 보안에 내재 되어 있습니다.

소비자가 용도를 지정 하는 경우 용도 문자열이 루트 암호화 키와 함께 사용 되어 해당 소비자에 고유한 암호화 하위 키를 파생 시킵니다. 이렇게 하면 다른 구성 요소에서 해당 페이로드를 읽을 수 없으며 다른 구성 요소의 페이로드를 읽을 수 없습니다. 이 격리를 통해 구성 요소에 대 한 부적합 한 전체 공격 범주가 렌더링 됩니다.

![목적 다이어그램 예제](purpose-strings/_static/purposes.png)

위의 다이어그램에서 `IDataProtector` A와 B 인스턴스는 서로의 페이로드를 읽을 **수 없습니다** .

용도 문자열은 비밀로 지정할 필요가 없습니다. 잘 작동 하는 다른 구성 요소는 동일한 용도 문자열을 제공 하지 않는다는 점에서 고유 해야 합니다.

>[!TIP]
> 데이터 보호 Api를 사용 하는 구성 요소의 네임 스페이스 및 형식 이름을 사용 하는 것이 좋은 방법입니다. 실제로이 정보는 충돌 하지 않습니다.
>
>Minting 전달자 토큰을 담당 하는 Contoso에서 작성 한 구성 요소는 목적 문자열로 BearerToken을 사용할 수 있습니다. 또는 더 잘 사용 가능 합니다. BearerToken를 용도 문자열로 사용할 수 있습니다. 버전 번호를 추가 하면 이후 버전에서 BearerToken를 목적으로 사용할 수 있으며, 다른 버전은 페이로드 이동 처럼 완전히 격리 됩니다.

에 대 한 용도 매개 변수가 문자열 배열 이기 때문에 `CreateProtector` 위의은 대신로 지정할 수 있습니다 `[ "Contoso.Security.BearerToken", "v1" ]` . 이를 통해 용도 계층을 설정 하 고 데이터 보호 시스템으로 다중 테 넌 트 시나리오의 가능성을 열 수 있습니다.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> 구성 요소는 신뢰할 수 없는 사용자 입력이 용도 체인의 유일한 입력 원본인 것을 허용 하지 않습니다.
>
>예를 들어 보안 메시지의 저장을 담당 하는 구성 요소 Contoso.com 메시지를 생각해 보세요. 보안 메시징 구성 요소가를 호출 하는 경우 `CreateProtector([ username ])` 악의적인 사용자가 호출 하는 구성 요소를 가져오기 위해 "BearerToken" 라는 사용자 이름이 있는 계정을 만들 수 있습니다 `CreateProtector([ "Contoso.Security.BearerToken" ])` . 따라서 보안 메시징 시스템은 인증 토큰으로 인식 될 수 있는 mint 페이로드의으로 발생 합니다.
>
>메시징 구성 요소에 대 한 더 나은 용도 체인은 `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` 적절 한 격리를 제공 하는입니다.

, 및의 동작에서 제공 하는 격리는 다음과 같습니다 `IDataProtectionProvider` `IDataProtector` .

* 지정 된 개체에 대해 `IDataProtectionProvider` 메서드는 개체를 `CreateProtector` `IDataProtector` `IDataProtectionProvider` 만든 개체와 메서드에 전달 된 용도 매개 변수 모두에 고유 하 게 연결 된 개체를 만듭니다.

* 목적 매개 변수는 null이 아니어야 합니다. (용도가 배열로 지정 된 경우 배열의 길이가 0이 아니어야 하 고 배열의 모든 요소가 null이 아니어야 함을 의미 합니다.) 빈 문자열 용도는 기술적으로 허용 되지만 권장 되지 않습니다.

* 두 가지 용도 인수는 동일한 순서로 동일한 문자열을 포함 하는 경우에만 동일 합니다 (서 수 비교자 사용). 단일 용도의 인수는 해당 하는 단일 요소 용도의 배열과 동일 합니다.

* 두 `IDataProtector` 개체는 동일한 용도 매개 변수를 사용 하 여 동일한 개체에서 만들어진 경우에만 동일 `IDataProtectionProvider` 합니다.

* 지정 된 개체의 경우에 대 한 `IDataProtector` 호출은 `Unprotect(protectedData)` `unprotectedData` `protectedData := Protect(unprotectedData)` 해당 개체에 대 한 인 경우에만 원래를 반환 합니다 `IDataProtector` .

> [!NOTE]
> 일부 구성 요소는 다른 구성 요소와 충돌 하는 것으로 알려진 목적 문자열을 의도적으로 선택 하는 경우를 고려 하지 않습니다. 이러한 구성 요소는 기본적으로 악의적인 것으로 간주 되며,이 시스템은 악의적인 코드가 작업자 프로세스 내에서 이미 실행 되 고 있는 경우 보안을 보장 하기 위한 것이 아닙니다.
