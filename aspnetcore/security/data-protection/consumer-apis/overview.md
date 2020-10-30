---
title: ASP.NET Core에 대 한 소비자 Api 개요
author: rick-anderson
description: ASP.NET Core 데이터 보호 라이브러리에서 사용할 수 있는 다양 한 소비자 Api에 대 한 간략 한 개요를 수신 합니다.
ms.author: riande
ms.date: 06/11/2019
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
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 485ea3f669b518f2979d04493b281bd116b05f65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051878"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core에 대 한 소비자 Api 개요

`IDataProtectionProvider`및 `IDataProtector` 인터페이스는 소비자가 데이터 보호 시스템을 사용 하는 기본 인터페이스입니다. 이러한 데이터는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 패키지에 있습니다.

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

공급자 인터페이스는 데이터 보호 시스템의 루트를 나타냅니다. 데이터를 보호 하거나 보호 해제 하는 데 직접 사용할 수 없습니다. 대신 소비자는를 호출 하 여에 대 한 참조를 가져와야 합니다 `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` . 여기서 용도는 의도 된 소비자 사용 사례를 설명 하는 문자열입니다. 이 매개 변수의 의도 및 적절 한 값을 선택 하는 방법에 대 한 자세한 내용은 [용도 문자열](xref:security/data-protection/consumer-apis/purpose-strings) 을 참조 하세요.

## <a name="idataprotector"></a>IDataProtector

보호기 인터페이스는를 호출 하 여 반환 `CreateProtector` 되며, 소비자는이 인터페이스를 사용 하 여 보호 및 보호 해제 작업을 수행할 수 있습니다.

데이터를 보호 하려면 메서드에 데이터를 전달 `Protect` 합니다. 기본 인터페이스는 byte []-> byte []를 변환 하는 메서드를 정의 하지만 문자열 > 문자열을 변환 하는 오버 로드 (확장 메서드로 제공 됨)도 있습니다. 두 메서드에서 제공 하는 보안은 동일 합니다. 개발자는 사용 사례에 가장 편리한 오버 로드를 선택 해야 합니다. 선택한 오버 로드에 관계 없이 Protect 메서드에서 반환 된 값은 이제 보호 되 고 (암호화 된 읽거나 및 proofed) 응용 프로그램은 신뢰할 수 없는 클라이언트에 보낼 수 있습니다.

이전에 보호 된 데이터 부분의 보호를 해제 하려면 보호 된 데이터를 메서드에 전달 합니다 `Unprotect` . 개발자 편의를 위해 바이트 [] 기반 및 문자열 기반 오버 로드가 있습니다. 이에 대 한 이전 호출에 의해 보호 된 페이로드가 생성 된 경우 `Protect` 이 `IDataProtector` `Unprotect` 메서드는 보호 되지 않는 원래 페이로드를 반환 합니다. 보호 된 페이로드가 변조 되었거나 다른에 의해 생성 된 경우이 `IDataProtector` 메서드는 system.security.cryptography.cryptographicexception을 `Unprotect` throw 합니다.

동일한 개념과 다른의 개념은 `IDataProtector` 목적 개념에 다시 연결 됩니다. 두 `IDataProtector` 인스턴스가 동일한 루트에서 생성 `IDataProtectionProvider` 되었지만에 대 한 호출에서 다른 용도의 문자열을 통해 생성 된 경우 `IDataProtectionProvider.CreateProtector` [다른 보호기](xref:security/data-protection/consumer-apis/purpose-strings)로 간주 되 고 다른 인스턴스가 생성 한 페이로드를 보호 해제할 수 없습니다.

## <a name="consuming-these-interfaces"></a>이러한 인터페이스 사용

DI 인식 구성 요소의 경우 구성 요소가 `IDataProtectionProvider` 생성자에서 매개 변수를 사용 하 고 구성 요소가 인스턴스화될 때 di 시스템이이 서비스를 자동으로 제공 한다는 것입니다.

> [!NOTE]
> 일부 응용 프로그램 (예: 콘솔 응용 프로그램 또는 ASP.NET 4.x 응용 프로그램)은 DI를 인식 하지 못할 수 있으므로 여기에 설명 된 메커니즘을 사용할 수 없습니다. 이러한 시나리오에서는 DI를 거치지 않고 공급자의 인스턴스를 가져오는 방법에 대 한 자세한 내용은 [비 Di 인식 시나리오](xref:security/data-protection/configuration/non-di-scenarios) 문서를 참조 하세요 `IDataProtection` .

다음 샘플에서는 세 가지 개념을 보여 줍니다.

1. 서비스 컨테이너에 [데이터 보호 시스템 추가](xref:security/data-protection/configuration/overview)

2. DI를 사용 하 여 인스턴스 받기 `IDataProtectionProvider` 및

3. 에서을 만들고 `IDataProtector` `IDataProtectionProvider` 이를 사용 하 여 데이터 보호 및 보호 해제

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

AspNetCore 패키지에는 개발자 편의를 위한 확장 메서드가 포함 되어 있습니다. `IServiceProvider.GetDataProtector` 서비스 공급자에서를 검색 하 고를 호출 하는 단일 작업으로 캡슐화 `IDataProtectionProvider` `IDataProtectionProvider.CreateProtector` 합니다. 다음 샘플에서는 사용 방법을 보여 줍니다.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> 및 인스턴스 `IDataProtectionProvider` `IDataProtector` 는 여러 호출자가 스레드로부터 안전 하 게 보호 됩니다. 구성 요소가 호출을 통해에 대 한 참조를 가져온 후에 `IDataProtector` `CreateProtector` 는 및에 대 한 여러 호출에 해당 참조를 사용 합니다 `Protect` `Unprotect` . `Unprotect`보호 된 페이로드를 확인 하거나 해독할 수 없는 경우에 대 한 호출은 system.security.cryptography.cryptographicexception을 throw 합니다. 일부 구성 요소는 보호 되지 않는 작업 중에 오류를 무시 하려고 할 수 있습니다. 인증을 읽는 구성 요소가 cookie 이 오류를 처리 하 고 요청을 완전히 실패 하는 것이 아닌 모든 요청을 처리 하는 것 처럼 처리할 수 있습니다 cookie . 이 동작을 원하는 구성 요소는 모든 예외를 swallowing 하는 대신 System.security.cryptography.cryptographicexception를 명확 하 게 catch 해야 합니다.
