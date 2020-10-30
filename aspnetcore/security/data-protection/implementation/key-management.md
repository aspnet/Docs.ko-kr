---
title: ASP.NET Core의 키 관리
author: rick-anderson
description: ASP.NET Core Data Protection key management Api에 대 한 구현 세부 정보를 알아봅니다.
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: f7e0fa5e80208469188a11239a9453ecafdcf5f3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060198"
---
# <a name="key-management-in-aspnet-core"></a>ASP.NET Core의 키 관리

<a name="data-protection-implementation-key-management"></a>

데이터 보호 시스템은 페이로드를 보호 하 고 보호를 해제 하는 데 사용 되는 마스터 키의 수명을 자동으로 관리 합니다. 각 키는 다음 네 단계 중 하나에 있을 수 있습니다.

* Created-키가 키 링에 있지만 아직 활성화 되지 않았습니다. 키가이 키 링을 사용 하는 모든 컴퓨터에 전파 될 수 있는 충분 한 시간이 경과할 때까지 새 보호 작업에 키를 사용 하면 안 됩니다.

* 활성-키 링에 키가 있으며 모든 새 보호 작업에 사용 해야 합니다.

* 만료 됨-키가 기본 수명을 실행 하 고 새 보호 작업에 더 이상 사용 되지 않습니다.

* 해지 됨-키가 손상 되어 새 보호 작업에 사용 되지 않아야 합니다.

생성, 활성 및 만료 된 키는 모두 들어오는 페이로드의 보호를 해제 하는 데 사용할 수 있습니다. 기본적으로 해지 된 키를 사용 하 여 페이로드를 보호 하지 않을 수 있지만, 필요한 경우 응용 프로그램 개발자가 [이 동작을 재정의할](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) 수 있습니다.

>[!WARNING]
> 개발자는 키 링 (예: 파일 시스템에서 해당 파일 삭제)에서 키를 삭제 하려고 할 수 있습니다. 이 시점에서 키로 보호 되는 모든 데이터는 영구적으로 undecipherable 해지 된 키가 있는 것 처럼 비상 재정의가 없습니다. 키를 삭제 하는 것은 진정한 파괴적인 동작 이므로 데이터 보호 시스템은이 작업을 수행 하기 위한 최고 수준의 API를 노출 하지 않습니다.

## <a name="default-key-selection"></a>기본 키 선택

데이터 보호 시스템은 백업 리포지토리에서 키 링을 읽을 때 키 링에서 "기본" 키를 찾으려고 시도 합니다. 기본 키는 새 보호 작업에 사용 됩니다.

일반적인 추론은 데이터 보호 시스템이 가장 최근의 활성화 날짜를 기본 키로 사용 하 여 키를 선택 하는 것입니다. 서버 간 클럭 오차를 허용 하는 작은 fudge 요인이 있습니다. 키가 만료 되거나 해지 된 경우 및 응용 프로그램에서 자동 키 생성을 사용 하지 않도록 설정 하지 않은 경우 아래의 [키 만료 및 롤링](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) 정책에 따라 즉시 활성화 하 여 새 키가 생성 됩니다.

데이터 보호 시스템에서 다른 키로 대체 하는 대신 새 키를 즉시 생성 하는 이유는 새 키 생성이 새 키 이전에 활성화 된 모든 키의 암시적 만료로 처리 되어야 한다는 것입니다. 일반적인 개념은 새 키가 이전 키 보다 다른 알고리즘 또는 미사용 암호화 메커니즘을 사용 하 여 구성 된 것일 수 있으며 시스템은 현재 구성을 사용 하는 것이 좋습니다.

예외가 있습니다. 응용 프로그램 개발자가 [자동 키 생성을 사용 하지 않도록](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)설정한 경우 데이터 보호 시스템은 기본 키로 항목을 선택 해야 합니다. 이 대체 시나리오에서 시스템은 가장 최근의 활성화 날짜를 사용 하 여 해지 되지 않은 키를 선택 하 고, 클러스터의 다른 컴퓨터로 전파 하는 데 시간이 오래 걸리는 키에 대 한 기본 설정을 지정 합니다. 대체 (fallback) 시스템에서 만료 된 기본 키를 선택할 수 있습니다. 대체 (fallback) 시스템은 해지 된 키를 기본 키로 선택 하지 않으며, 키 링이 비어 있거나 모든 키가 해지 된 경우 초기화 시 시스템에서 오류를 생성 합니다.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>키 만료 및 롤링

키를 만들 때 자동으로 활성화 날짜를 {now + 2 days}로 지정 하 고 만료 날짜를 {now + 90 days}로 지정 합니다. 활성화 하기 전 2 일 지연 시간은 시스템을 통해 전파 되는 키 시간을 제공 합니다. 즉, 백업 저장소를 가리키는 다른 응용 프로그램이 다음 자동 새로 고침 기간에 키를 관찰할 수 있으므로 키 링이 활성화 될 때이를 사용 해야 할 수 있는 모든 응용 프로그램에 전파 될 가능성을 극대화할 수 있습니다.

기본 키가 2 일 이내에 만료 되 고 키 링에 기본 키가 만료 될 때 활성화 되는 키가 아직 없는 경우 데이터 보호 시스템은 자동으로 키 링에 새 키를 유지 합니다. 이 새 키에는 {기본 키의 만료 날짜}의 활성화 날짜와 {now + 90 days}의 만료 날짜가 있습니다. 이렇게 하면 시스템에서 서비스 중단 없이 정기적으로 키를 자동으로 롤백할 수 있습니다.

즉시 활성화를 사용 하 여 키가 생성 되는 경우가 있을 수 있습니다. 한 가지 예는 응용 프로그램이 한 번에 실행 되지 않았고 키 링의 모든 키가 만료 된 경우입니다. 이 경우 정상 2 일 활성화 지연 없이 키에 {now}의 활성화 날짜가 지정 됩니다.

기본 키 수명은 90 일 이지만 다음 예제와 같이 구성할 수 있습니다.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

에 대 한 명시적 호출이 `SetDefaultKeyLifetime` 시스템 차원의 정책을 재정의 하더라도 관리자는 기본 시스템 전체를 변경할 수도 있습니다. 기본 키 수명은 7 일 보다 짧을 수 없습니다.

## <a name="automatic-key-ring-refresh"></a>자동 키 링 새로 고침

데이터 보호 시스템이 초기화 되 면 기본 리포지토리에서 키 링을 읽고 메모리에 캐시 합니다. 이 캐시를 사용 하면 보호 및 보호 해제 작업이 백업 저장소에 도달 하지 않고 계속 진행 됩니다. 시스템은 백업 저장소에서 약 24 시간 마다 또는 현재 기본 키가 만료 되는 시간 중 먼저 발생 하는 시간에 대 한 변경 내용을 자동으로 검사 합니다.

>[!WARNING]
> 개발자는 키 관리 Api를 직접 사용 해야 하는 경우는 거의 없습니다. 데이터 보호 시스템은 위에서 설명한 대로 자동 키 관리를 수행 합니다.

데이터 보호 시스템은 `IKeyManager` 키 링을 검사 하 고 변경 하는 데 사용할 수 있는 인터페이스를 노출 합니다. 인스턴스를 제공한 DI 시스템은 사용 `IDataProtectionProvider` 에 대 한 인스턴스를 제공할 수도 있습니다 `IKeyManager` . 또는 `IKeyManager` `IServiceProvider` 아래 예제와 같이에서 직접를 끌어올 수 있습니다.

키 링을 수정 하는 모든 작업 (명시적으로 또는 해지를 수행 하는 새 키를 만드는 작업)은 메모리 내 캐시를 무효화 합니다. 또는에 대 한 다음 호출로 `Protect` `Unprotect` 인해 데이터 보호 시스템이 키 링을 다시 읽고 캐시를 다시 만듭니다.

아래 샘플에서는 기존 키를 취소 하 `IKeyManager` 고 수동으로 새 키를 생성 하는 등의 방법으로 인터페이스를 사용 하 여 키 링을 검사 및 조작 하는 방법을 보여 줍니다.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>키 스토리지

데이터 보호 시스템에는 적절 한 키 저장소 위치 및 암호화 미사용 메커니즘이 자동으로 추론 되도록 하는 추론 기능이 있습니다. 키 지 속성 메커니즘은 앱 개발자도 구성할 수 있습니다. 다음 문서에서는 이러한 메커니즘의 기본 구현에 대해 설명 합니다.

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
