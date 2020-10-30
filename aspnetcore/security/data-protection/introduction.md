---
title: ASP.NET Core 데이터 보호
author: rick-anderson
description: 데이터 보호의 개념과 ASP.NET Core 데이터 보호 Api의 디자인 원리에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/introduction
ms.openlocfilehash: 5fd5676b286e758f0648d78bf8cb4171e7a98f60
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051696"
---
# <a name="aspnet-core-data-protection"></a>ASP.NET Core 데이터 보호

웹 응용 프로그램은 보안에 중요 한 데이터를 저장 해야 하는 경우가 많습니다. Windows에서는 데스크톱 응용 프로그램에 대해 DPAPI를 제공 하지만 웹 응용 프로그램에는 적합 하지 않습니다. ASP.NET Core 데이터 보호 스택은 개발자가 키 관리 및 순환을 포함 하 여 데이터를 보호 하는 데 사용할 수 있는 간단 하 고 사용 하기 쉬운 암호화 API를 제공 합니다.

ASP.NET Core data protection stack은 ASP.NET 1.x-4.x. &lt; x에서 machineKey 요소에 대 한 장기 대체 역할을 하도록 디자인 되었습니다. &gt; 최신 응용 프로그램에서 발생할 수 있는 대부분의 사용 사례에 대 한 기본 제공 솔루션을 제공 하는 동시에 이전 암호화 스택의 많은 단점을 해결 하도록 설계 되었습니다.

## <a name="problem-statement"></a>문제 설명

전체 문제 설명은 한 문장으로 간략하게 될 수 있습니다. 나중에 검색할 수 있도록 신뢰할 수 있는 정보를 유지 해야 하지만 지 속성 메커니즘은 신뢰 하지 않습니다. 웹 용어로는 "신뢰할 수 없는 클라이언트를 통해 라운드트립 된 신뢰할 수 있는 상태 여야 합니다."로 작성 될 수 있습니다.

이에 대 한 정식 예는 인증 cookie 또는 전달자 토큰입니다. 서버는 "I am Groot 및 xyz 권한 보유" 토큰을 생성 하 고 클라이언트에 전달 합니다. 이후에 클라이언트는 해당 토큰을 서버에 다시 제공 하지만 서버는 클라이언트가 토큰을 위조 하지 못했음을 나타내는 일종의 보증을 요구 합니다. 따라서 첫 번째 요구 사항: 신뢰성 ( 무결성, 변조 방지).

서버에서 지속형 상태를 신뢰 하므로이 상태에는 운영 환경과 관련 된 정보가 포함 될 수 있습니다. 이는 파일 경로, 사용 권한, 핸들 또는 다른 간접 참조의 형식 이거나 서버 관련 데이터의 다른 부분에 있을 수 있습니다. 이러한 정보는 일반적으로 신뢰할 수 없는 클라이언트에 공개 되지 않습니다. 따라서 두 번째 요구 사항은 기밀입니다.

마지막으로, 최신 응용 프로그램은 구성 요소화 되었으므로 개별 구성 요소가 시스템의 다른 구성 요소와 상관 없이이 시스템을 활용 하려고 합니다. 예를 들어 전달자 토큰 구성 요소가이 스택을 사용 하는 경우 동일한 스택을 사용할 수도 있는 CSRF 메커니즘의 간섭 없이 작동 해야 합니다. 따라서 최종 요구 사항은 격리입니다.

요구 사항의 범위를 좁히기 위해 추가 제약 조건을 제공할 수 있습니다. Cryptosystem 내에서 작동 하는 모든 서비스는 동일한 신뢰를 받고 직접 제어 하는 서비스 외부에서 데이터를 생성 하거나 사용할 필요가 없는 것으로 가정 합니다. 또한 웹 서비스에 대 한 각 요청이 cryptosystem을 한 번 이상 통과할 수 있으므로 작업이 최대한 빠르게 수행 되어야 합니다. 이를 통해 시나리오에 적합 한 대칭 암호화를 사용할 수 있으며, 필요할 때까지 비대칭 암호화의 할인율을 사용할 수 있습니다.

## <a name="design-philosophy"></a>디자인 원칙

기존 스택의 문제를 식별 하 여 시작 했습니다. 이 작업이 완료 되 면 기존 솔루션의 가로를 조사 하 고 기존 솔루션이 아직 검색 한 기능을가지고 있지 않음을 결론 했습니다. 그런 다음 몇 가지 안내 원칙에 따라 솔루션을 설계 했습니다.

* 시스템은 구성을 간단 하 게 제공 해야 합니다. 시스템이 구성 되지 않은 것이 가장 이상적입니다. 개발자가 특정 측면 (예: 키 리포지토리)을 구성 해야 하는 경우 이러한 특정 구성을 간단 하 게 하기 위해 고려해 야 합니다.

* 간단한 소비자 지향 API를 제공 합니다. Api는 올바르게 사용 하기 쉽고 잘못 사용 하기 어렵습니다.

* 개발자는 키 관리 원칙을 배울 필요가 없습니다. 시스템은 개발자를 대신해 알고리즘 선택 및 키 수명을 처리 해야 합니다. 개발자는 원시 키 자료에 대 한 액세스 권한이 없어도 됩니다.

* 가능 하면 휴지 상태의 키를 보호 해야 합니다. 시스템은 적절 한 기본 보호 메커니즘을 파악 하 고 자동으로 적용 해야 합니다.

이러한 원칙을 염두에 두면 간단 하 고 [사용 하기 쉬운](xref:security/data-protection/using-data-protection) 데이터 보호 스택을 개발 했습니다.

ASP.NET Core 데이터 보호 Api는 주로 기밀 페이로드의 무한 지 속성에 적합 하지 않습니다. [WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi) 및 [Azure Rights Management](/rights-management/) 와 같은 기타 기술은 무한 저장소의 시나리오에 보다 적합 하며 강력한 키 관리 기능을 제공 합니다. 즉, 기밀 데이터의 장기 보호를 위해 개발자가 ASP.NET Core 데이터 보호 Api를 사용할 필요가 없습니다.

## <a name="audience"></a>사용자

데이터 보호 시스템은 5 개의 주 패키지로 나뉩니다. 이러한 Api의 다양 한 측면은 세 가지 주요 대상을 대상으로 합니다.

1. [소비자 Api 개요](xref:security/data-protection/consumer-apis/overview) 는 응용 프로그램 및 프레임 워크 개발자를 대상으로 합니다.

   "스택 작동 방법 또는 구성 방법에 대해 알아보는 것이 좋습니다. 단순히 Api를 성공적으로 사용 하는 확률이 매우 높은 간단한 방법으로에서 일부 작업을 수행 하려고 합니다. "

2. [구성 api](xref:security/data-protection/configuration/overview) 는 응용 프로그램 개발자 및 시스템 관리자를 대상으로 합니다.

   "데이터 보호 시스템에 기본 경로 또는 설정이 필요 하지 않습니다." 라는 정보를 제공 합니다.

3. 확장성 Api는 개발자가 사용자 지정 정책을 구현 하는 것을 담당 합니다. 이러한 Api를 사용 하는 것은 드문 상황 및 숙련 된 보안 개발자로 제한 됩니다.

   "시스템 내에서 전체 구성 요소를 교체 해야 합니다. 내 요구 사항을 충족 하는 플러그 인을 빌드하기 위해 API 표면의 일반적으로 사용 되지 않는 부분을 배워야 합니다. "

## <a name="package-layout"></a>패키지 레이아웃

데이터 보호 스택은 5 개의 패키지로 구성 됩니다.

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 는 <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 데이터 보호 서비스를 만들기 위한 및 인터페이스를 포함 합니다. 또한 이러한 형식 (예: [IDataProtector](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*))으로 작업 하는 데 유용한 확장 메서드가 포함 되어 있습니다. 데이터 보호 시스템이 다른 곳에서 인스턴스화되고 API를 사용 하는 경우 참조 `Microsoft.AspNetCore.DataProtection.Abstractions` 합니다.

* [AspNetCore 보호](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 는 핵심 암호화 작업, 키 관리, 구성 및 확장성을 포함 하 여 데이터 보호 시스템의 핵심 구현을 포함 합니다. 데이터 보호 시스템을 (예:에 추가 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ) 인스턴스화하거나 해당 동작을 수정 하거나 확장 하려면를 참조 `Microsoft.AspNetCore.DataProtection` 합니다.

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 에는 개발자가 유용 하지만 핵심 패키지에 속하지 않는 추가 api가 포함 되어 있습니다. 예를 들어이 패키지에는 데이터 보호 시스템을 인스턴스화하여 종속성 주입 없이 파일 시스템의 위치에 키를 저장 하는 팩터리 메서드가 포함 되어 있습니다 (참조 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> ). 또한 보호 된 페이로드의 수명을 제한 하는 확장 메서드도 포함 되어 있습니다 (참조 <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector> ).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) 를 기존 ASP.NET 4.x 앱에 설치 하 여 `<machineKey>` 새 ASP.NET Core 데이터 보호 스택을 사용 하도록 작업을 리디렉션할 수 있습니다. 자세한 내용은 <xref:security/data-protection/compatibility/replacing-machinekey>를 참조하세요.

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) 는 PBKDF2 암호 해시 루틴의 구현을 제공 하며, 사용자 암호를 안전 하 게 처리 해야 하는 시스템에서 사용할 수 있습니다. 자세한 내용은 <xref:security/data-protection/consumer-apis/password-hashing>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

<xref:host-and-deploy/web-farm>
