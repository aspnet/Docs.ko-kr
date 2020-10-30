---
title: ASP.NET Core에서 데이터 보호 키 관리 및 수명
author: rick-anderson
description: ASP.NET Core의 데이터 보호 키 관리 및 수명에 대해 알아봅니다.
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053009"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>ASP.NET Core에서 데이터 보호 키 관리 및 수명

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>키 관리

앱은 해당 운영 환경을 검색 하 고 자체에서 키 구성을 처리 하려고 합니다.

1. 앱이 [Azure 앱](https://azure.microsoft.com/services/app-service/)에서 호스트 되는 경우 키는 *%HOME%\ASP.NET\DataProtection-Keys* 폴더에 유지 됩니다. 이 폴더는 네트워크 스토리지에서 지원하고, 앱을 호스트하는 모든 머신에서 동기화됩니다.
   * 저장된 키는 보호되지 않습니다.
   * *Dataprotection-Keys* 폴더는 단일 배포 슬롯의 모든 앱 인스턴스에 대 한 키 링을 제공 합니다.
   * 준비 및 프로덕션과 같은 별도의 배포 슬롯은 키 링을 공유하지 않습니다. 스테이징을 프로덕션으로 교환 하거나 A/B 테스트를 사용 하는 경우와 같이 배포 슬롯 간에 교환 하는 경우 데이터 보호를 사용 하는 모든 앱은 이전 슬롯 내에서 키 링을 사용 하 여 저장 된 데이터의 암호를 해독할 수 없습니다. 이를 통해 사용자는 cookie 데이터 보호를 사용 하 여를 보호 하므로 표준 ASP.NET Core 인증을 사용 하는 앱에서 로그 아웃 됩니다 cookie . 슬롯 독립적 키 링을 원하는 경우 Azure Blob Storage, Azure Key Vault, SQL 저장소 또는 Redis cache와 같은 외부 키 링 공급자를 사용 합니다.

1. 사용자 프로필을 사용할 수 있는 경우 키가 *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys* 폴더에 유지 됩니다. 운영 체제가 Windows 인 경우 키는 암호화 된 상태로 DPAPI를 사용 하 여 암호화 됩니다.

   앱 풀의 [setProfileEnvironment 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다. `setProfileEnvironment` 의 기본값은 `true`입니다. Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다. 키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.

   1. *%windir%/system32/inetsrv/config* 폴더로 이동합니다.
   1. *applicationHost.config* 파일을 엽니다.
   1. `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.
   1. `setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.

1. 앱이 IIS에서 호스트 되는 경우 키는 작업자 프로세스 계정에만 해당 되는 특수 레지스트리 키로 HKLM 레지스트리에 유지 됩니다. 미사용 키는 DPAPI를 사용하여 암호화됩니다.

1. 이러한 조건에 일치 하는 항목이 없는 경우 키는 현재 프로세스 외부에서 유지 되지 않습니다. 프로세스가 종료 되 면 생성 된 모든 키가 손실 됩니다.

개발자는 항상 모든 권한을 가지 며, 키가 저장 되는 방법과 위치를 재정의할 수 있습니다. 위의 처음 세 옵션은 ASP.NET **\<machineKey>** 자동 생성 루틴이 과거에 작업 하는 방식과 유사한 대부분의 앱에 대해 좋은 기본값을 제공 해야 합니다. 최종, 대체 (fallback) 옵션은 개발자가 키 지 속성을 원할 경우 [구성을](xref:security/data-protection/configuration/overview) 사전에 지정 해야 하는 유일한 시나리오 이지만 이러한 대체는 드문 경우에만 발생 합니다.

Docker 컨테이너에서 호스트 하는 경우 키는 Docker 볼륨 (컨테이너의 수명 이상으로 지속 되는 공유 볼륨 또는 호스트에서 탑재 된 볼륨) 또는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 또는 [Redis](https://redis.io/)와 같은 외부 공급자에 있는 폴더에 유지 되어야 합니다. 외부 공급자는 앱이 공유 네트워크 볼륨에 액세스할 수 없는 경우 웹 팜 시나리오에도 유용 합니다 (자세한 내용은 [Persistkeystofilesystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) 참조).

> [!WARNING]
> 개발자가 위에 설명 된 규칙을 재정의 하 고 특정 키 리포지토리에서 데이터 보호 시스템을 가리키면 미사용 키의 자동 암호화를 사용할 수 없습니다. Rest 보호는 [구성을](xref:security/data-protection/configuration/overview)통해 다시 사용 하도록 설정할 수 있습니다.

## <a name="key-lifetime"></a>키 수명

키의 수명은 기본적으로 90 일입니다. 키가 만료 되 면 앱은 자동으로 새 키를 생성 하 고 새 키를 활성 키로 설정 합니다. 사용 중지 된 키가 시스템에 남아 있으면 앱이 해당 키로 보호 되는 데이터의 암호를 해독할 수 있습니다. 자세한 내용은 [키 관리](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) 를 참조 하세요.

## <a name="default-algorithms"></a>기본 알고리즘

사용 되는 기본 페이로드 보호 알고리즘은 기밀성을 위한 AES-256-HMACSHA256 및 정품 인증입니다. 90 일 마다 변경 된 512 비트 마스터 키는 페이로드에 따라 이러한 알고리즘에 사용 되는 두 개의 하위 키를 파생 하는 데 사용 됩니다. 자세한 내용은 [하위 키 파생](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) 을 참조 하십시오.

## <a name="additional-resources"></a>추가 리소스

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
