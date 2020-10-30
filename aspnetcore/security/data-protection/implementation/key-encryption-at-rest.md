---
title: ASP.NET Core를 사용 하 여 Windows 및 Azure에서 휴지 상태의 키 암호화
author: rick-anderson
description: 미사용 데이터 보호 키 암호화 ASP.NET Core의 구현 세부 정보에 대해 알아봅니다.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061277"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>ASP.NET Core를 사용 하 여 Windows 및 Azure에서 휴지 상태의 키 암호화

데이터 보호 시스템은 [기본적으로 검색 메커니즘을 채택](xref:security/data-protection/configuration/default-settings) 하 여 암호화 키를 암호화 하는 방법을 결정 합니다. 개발자는 검색 메커니즘을 재정의 하 고 미사용 키를 암호화 하는 방법을 수동으로 지정할 수 있습니다.

> [!WARNING]
> 명시적 [키 지 속성 위치](xref:security/data-protection/implementation/key-storage-providers)를 지정 하는 경우 데이터 보호 시스템은 미사용 기본 키 암호화 메커니즘을 등록 취소 합니다. 따라서 더 이상 미사용에서 키가 암호화 되지 않습니다. 프로덕션 배포를 위한 [명시적 키 암호화 메커니즘을 지정](xref:security/data-protection/implementation/key-encryption-at-rest) 하는 것이 좋습니다. 미사용 암호화 메커니즘 옵션은이 항목에 설명 되어 있습니다.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 키를 저장 하려면 클래스에서 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 를 사용 하 여 시스템을 구성 합니다 `Startup` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

자세한 내용은 [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)을 참조 하세요.

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Windows 배포에만 적용 됩니다.**

Windows DPAPI를 사용 하는 경우 키 자료는 저장소에 유지 되기 전에 [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) 를 사용 하 여 암호화 됩니다. DPAPI는 현재 컴퓨터 외부에서 읽지 않는 데이터에 대 한 적절 한 암호화 메커니즘입니다. 이러한 키는 Active Directory까지 백업할 수 있지만 [dpapi 및 로밍 프로필](https://support.microsoft.com/kb/309408/#6)을 참조 하세요. DPAPI에서 rest로 암호화를 구성 하려면 [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) 확장 메서드 중 하나를 호출 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

`ProtectKeysWithDpapi`매개 변수 없이를 호출 하면 현재 Windows 사용자 계정만 지속형 키 링을 해독할 수 있습니다. 필요에 따라 현재 사용자 계정 뿐만 아니라 컴퓨터의 모든 사용자 계정이 키 링을 해독할 수 있도록 지정할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X.509 인증서

앱이 여러 컴퓨터에 분산 되어 있는 경우 컴퓨터 간에 공유 된 x.509 인증서를 배포 하 고 미사용 키의 암호화에 인증서를 사용 하도록 호스트 된 앱을 구성 하는 것이 편리할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

.NET Framework 제한으로 인해 CAPI 개인 키가 있는 인증서만 지원 됩니다. 이러한 제한 사항에 대 한 가능한 해결 방법은 아래 콘텐츠를 참조 하세요.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-기능

**이 메커니즘은 Windows 8/Windows Server 2012 이상 에서만 사용할 수 있습니다.**

Windows 8부터 Windows OS는 DPAPI (CNG DPAPI 라고도 함)를 지원 합니다. 자세한 내용은 [CNG DPAPI 정보](/windows/desktop/SecCNG/cng-dpapi)를 참조 하세요.

보안 주체는 보호 설명자 규칙으로 인코딩됩니다. [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)를 호출 하는 다음 예제에서는 지정 된 SID가 있는 도메인에 가입 된 사용자만 키 링의 암호를 해독할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

매개 변수가 없는 오버 로드도 있습니다 `ProtectKeysWithDpapiNG` . 이 편리한 방법을 사용 하 여 "SID = {CURRENT_ACCOUNT_SID}" 규칙을 지정 합니다. 여기서 *CURRENT_ACCOUNT_SID* 은 현재 Windows 사용자 계정의 SID입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

이 시나리오에서 AD 도메인 컨트롤러는 DPAPI를 사용 하는 작업에서 사용 하는 암호화 키를 배포 합니다. 대상 사용자는 해당 id로 프로세스가 실행 되는 경우 도메인에 가입 된 컴퓨터에서 암호화 된 페이로드를 해독할 수 있습니다.

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Windows DPAPI를 사용 하는 인증서 기반 암호화

앱이 Windows 8.1/Windows Server 2012 R2 이상에서 실행 되는 경우 Windows DPAPI를 사용 하 여 인증서 기반 암호화를 수행할 수 있습니다. 규칙 설명자 문자열 "CERTIFICATE = HashId: THUMBPRINT"를 사용 합니다. 여기서 *THUMBPRINT* 는 인증서의 16 진수로 인코딩된 SHA1 지문입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

키를 해독 하려면이 리포지토리를 가리키는 모든 앱이 Windows 8.1/Windows Server 2012 R2 이상에서 실행 되 고 있어야 합니다.

## <a name="custom-key-encryption"></a>사용자 지정 키 암호화

기본 제공 메커니즘이 적절 하지 않은 경우 개발자는 사용자 지정 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)을 제공 하 여 고유한 키 암호화 메커니즘을 지정할 수 있습니다.
