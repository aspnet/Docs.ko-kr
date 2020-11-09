---
title: ASP.NET Core를 사용 하 여 Windows 및 Azure에서 휴지 상태의 키 암호화
author: rick-anderson
description: 미사용 데이터 보호 키 암호화 ASP.NET Core의 구현 세부 정보에 대해 알아봅니다.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061277"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="e5582-103">ASP.NET Core를 사용 하 여 Windows 및 Azure에서 휴지 상태의 키 암호화</span><span class="sxs-lookup"><span data-stu-id="e5582-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="e5582-104">데이터 보호 시스템은 [기본적으로 검색 메커니즘을 채택](xref:security/data-protection/configuration/default-settings) 하 여 암호화 키를 암호화 하는 방법을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="e5582-105">개발자는 검색 메커니즘을 재정의 하 고 미사용 키를 암호화 하는 방법을 수동으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="e5582-106">명시적 [키 지 속성 위치](xref:security/data-protection/implementation/key-storage-providers)를 지정 하는 경우 데이터 보호 시스템은 미사용 기본 키 암호화 메커니즘을 등록 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="e5582-107">따라서 더 이상 미사용에서 키가 암호화 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="e5582-108">프로덕션 배포를 위한 [명시적 키 암호화 메커니즘을 지정](xref:security/data-protection/implementation/key-encryption-at-rest) 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="e5582-109">미사용 암호화 메커니즘 옵션은이 항목에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="e5582-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="e5582-110">Azure Key Vault</span></span>

<span data-ttu-id="e5582-111">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 키를 저장 하려면 클래스에서 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 를 사용 하 여 시스템을 구성 합니다 `Startup` .</span><span class="sxs-lookup"><span data-stu-id="e5582-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="e5582-112">자세한 내용은 [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e5582-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="e5582-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="e5582-113">Windows DPAPI</span></span>

<span data-ttu-id="e5582-114">**Windows 배포에만 적용 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="e5582-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="e5582-115">Windows DPAPI를 사용 하는 경우 키 자료는 저장소에 유지 되기 전에 [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) 를 사용 하 여 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="e5582-116">DPAPI는 현재 컴퓨터 외부에서 읽지 않는 데이터에 대 한 적절 한 암호화 메커니즘입니다. 이러한 키는 Active Directory까지 백업할 수 있지만 [dpapi 및 로밍 프로필](https://support.microsoft.com/kb/309408/#6)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e5582-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="e5582-117">DPAPI에서 rest로 암호화를 구성 하려면 [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) 확장 메서드 중 하나를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="e5582-118">`ProtectKeysWithDpapi`매개 변수 없이를 호출 하면 현재 Windows 사용자 계정만 지속형 키 링을 해독할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="e5582-119">필요에 따라 현재 사용자 계정 뿐만 아니라 컴퓨터의 모든 사용자 계정이 키 링을 해독할 수 있도록 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="e5582-120">X.509 인증서</span><span class="sxs-lookup"><span data-stu-id="e5582-120">X.509 certificate</span></span>

<span data-ttu-id="e5582-121">앱이 여러 컴퓨터에 분산 되어 있는 경우 컴퓨터 간에 공유 된 x.509 인증서를 배포 하 고 미사용 키의 암호화에 인증서를 사용 하도록 호스트 된 앱을 구성 하는 것이 편리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="e5582-122">.NET Framework 제한으로 인해 CAPI 개인 키가 있는 인증서만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="e5582-123">이러한 제한 사항에 대 한 가능한 해결 방법은 아래 콘텐츠를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e5582-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="e5582-124">Windows DPAPI-기능</span><span class="sxs-lookup"><span data-stu-id="e5582-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="e5582-125">**이 메커니즘은 Windows 8/Windows Server 2012 이상 에서만 사용할 수 있습니다.**</span><span class="sxs-lookup"><span data-stu-id="e5582-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="e5582-126">Windows 8부터 Windows OS는 DPAPI (CNG DPAPI 라고도 함)를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="e5582-127">자세한 내용은 [CNG DPAPI 정보](/windows/desktop/SecCNG/cng-dpapi)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="e5582-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="e5582-128">보안 주체는 보호 설명자 규칙으로 인코딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="e5582-129">[ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)를 호출 하는 다음 예제에서는 지정 된 SID가 있는 도메인에 가입 된 사용자만 키 링의 암호를 해독할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="e5582-130">매개 변수가 없는 오버 로드도 있습니다 `ProtectKeysWithDpapiNG` .</span><span class="sxs-lookup"><span data-stu-id="e5582-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="e5582-131">이 편리한 방법을 사용 하 여 "SID = {CURRENT_ACCOUNT_SID}" 규칙을 지정 합니다. 여기서 *CURRENT_ACCOUNT_SID* 은 현재 Windows 사용자 계정의 SID입니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="e5582-132">이 시나리오에서 AD 도메인 컨트롤러는 DPAPI를 사용 하는 작업에서 사용 하는 암호화 키를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="e5582-133">대상 사용자는 해당 id로 프로세스가 실행 되는 경우 도메인에 가입 된 컴퓨터에서 암호화 된 페이로드를 해독할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="e5582-134">Windows DPAPI를 사용 하는 인증서 기반 암호화</span><span class="sxs-lookup"><span data-stu-id="e5582-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="e5582-135">앱이 Windows 8.1/Windows Server 2012 R2 이상에서 실행 되는 경우 Windows DPAPI를 사용 하 여 인증서 기반 암호화를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="e5582-136">규칙 설명자 문자열 "CERTIFICATE = HashId: THUMBPRINT"를 사용 합니다. 여기서 *THUMBPRINT* 는 인증서의 16 진수로 인코딩된 SHA1 지문입니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="e5582-137">키를 해독 하려면이 리포지토리를 가리키는 모든 앱이 Windows 8.1/Windows Server 2012 R2 이상에서 실행 되 고 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="e5582-138">사용자 지정 키 암호화</span><span class="sxs-lookup"><span data-stu-id="e5582-138">Custom key encryption</span></span>

<span data-ttu-id="e5582-139">기본 제공 메커니즘이 적절 하지 않은 경우 개발자는 사용자 지정 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)을 제공 하 여 고유한 키 암호화 메커니즘을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e5582-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
