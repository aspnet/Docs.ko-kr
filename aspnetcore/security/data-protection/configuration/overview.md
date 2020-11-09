---
title: ASP.NET Core 데이터 보호 구성
author: rick-anderson
description: ASP.NET Core에서 데이터 보호를 구성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 048f6d6d57d3cc5d64004e18b18a3347ee92e450
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234571"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="78f2f-103">ASP.NET Core 데이터 보호 구성</span><span class="sxs-lookup"><span data-stu-id="78f2f-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="78f2f-104">데이터 보호 시스템이 초기화 되 면 운영 환경을 기반으로 [기본 설정이](xref:security/data-protection/configuration/default-settings) 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="78f2f-105">이러한 설정은 일반적으로 단일 컴퓨터에서 실행 되는 앱에 적합 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="78f2f-106">개발자가 기본 설정을 변경 해야 할 수 있는 경우는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="78f2f-107">앱이 여러 컴퓨터에 걸쳐 분산 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="78f2f-108">규정 준수 상의 이유로.</span><span class="sxs-lookup"><span data-stu-id="78f2f-108">For compliance reasons.</span></span>

<span data-ttu-id="78f2f-109">이러한 시나리오의 경우 데이터 보호 시스템은 다양 한 구성 API를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="78f2f-110">구성 파일과 마찬가지로 데이터 보호 키 링은 적절 한 권한을 사용 하 여 보호 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="78f2f-111">미사용 키를 암호화 하도록 선택할 수 있지만 공격자가 새 키를 만들 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="78f2f-112">따라서 앱의 보안에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="78f2f-113">데이터 보호를 사용 하 여 구성 된 저장소 위치는 구성 파일을 보호 하는 방식과 비슷하게 앱 자체에 대 한 액세스를 제한 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="78f2f-114">예를 들어 디스크에 키 링을 저장 하도록 선택 하는 경우 파일 시스템 권한을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="78f2f-115">웹 앱이 실행 되는 id에만 해당 디렉터리에 대 한 읽기, 쓰기 및 만들기 권한이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="78f2f-116">Azure Blob Storage 사용 하는 경우 웹 앱에만 Blob 저장소에서 새 항목을 읽거나, 쓰고, 만들 수 있는 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="78f2f-117">확장 메서드 [Adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) 은 [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="78f2f-118">`IDataProtectionBuilder` 데이터 보호 옵션을 구성 하기 위해 함께 연결할 수 있는 확장 메서드를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78f2f-119">이 문서에서 사용 되는 데이터 보호 확장에는 다음 NuGet 패키지가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="78f2f-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span><span class="sxs-lookup"><span data-stu-id="78f2f-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [<span data-ttu-id="78f2f-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span><span class="sxs-lookup"><span data-stu-id="78f2f-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="78f2f-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="78f2f-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="78f2f-123">CLI를 사용 하 여 Azure에 로그인 합니다. 예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-123">Log in to Azure using the CLI, for example:</span></span>

```azurecli
az login
``` 

<span data-ttu-id="78f2f-124">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 키를 저장 하려면 클래스에서 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 를 사용 하 여 시스템을 구성 `Startup` 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-124">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class.</span></span> <span data-ttu-id="78f2f-125">`blobUriWithSasToken` 키 파일을 저장 해야 하는 전체 URI입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-125">`blobUriWithSasToken` is the full URI where the key file should be stored.</span></span> <span data-ttu-id="78f2f-126">URI에는 SAS 토큰이 쿼리 문자열 매개 변수로 포함 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-126">The URI must contain the SAS token as a query string parameter:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

<span data-ttu-id="78f2f-127">키 링 저장소 위치 (예: [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage))를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-127">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="78f2f-128">를 호출 하면 `ProtectKeysWithAzureKeyVault` 키 링 저장소 위치를 포함 하 여 자동 데이터 보호 설정을 사용 하지 않도록 설정 하는 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) 을 구현 하기 때문에 위치를 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-128">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="78f2f-129">앞의 예제에서는 Azure Blob Storage를 사용 하 여 키 링을 유지 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-129">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="78f2f-130">자세한 내용은 [키 저장소 공급자: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="78f2f-130">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="78f2f-131">[Persistkeystofilesystem](xref:security/data-protection/implementation/key-storage-providers#file-system)을 사용 하 여 키 링을 로컬로 유지할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-131">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="78f2f-132">는 `keyIdentifier` 키 암호화에 사용 되는 키 자격 증명 모음 키 식별자입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-132">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="78f2f-133">예를 들어, 키 자격 증명 모음에서 만든 키의 키 식별자는 다음과 `dataprotection` `contosokeyvault` 같습니다 `https://contosokeyvault.vault.azure.net/keys/dataprotection/` .</span><span class="sxs-lookup"><span data-stu-id="78f2f-133">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="78f2f-134">키 **래핑** 및 키 자격 증명 모음에 대 한 **키 래핑 권한을** 앱에 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-134">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="78f2f-135">`ProtectKeysWithAzureKeyVault` 같도록</span><span class="sxs-lookup"><span data-stu-id="78f2f-135">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="78f2f-136">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, Uri, tokencredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 를 사용 하면 keyIdentifier Uri 및 tokencredential을 사용 하 여 데이터 보호 시스템에서 키 자격 증명 모음을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-136">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier Uri and a tokenCredential to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="78f2f-137">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, string, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 를 사용 하면 KeyIdentifier 문자열과 IKeyEncryptionKeyResolver를 사용 하 여 데이터 보호 시스템에서 키 자격 증명 모음을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-137">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, string, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier string and IKeyEncryptionKeyResolver to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="78f2f-138">앱이 이전 Azure 패키지 (및)를 사용 하 고 키를 [`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault) 저장 하 고 보호 하는 Azure Key Vault 및 Azure Storage의 조합을 사용 하는 경우 <xref:System.UriFormatException?displayProperty=nameWithType> 키 저장소에 대 한 blob이 존재 하지 않으면이 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-138">If the app uses the prior Azure packages ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) and [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) and a combination of Azure Key Vault and Azure Storage to store and protect keys, <xref:System.UriFormatException?displayProperty=nameWithType> is thrown if the blob for key storage doesn't exist.</span></span> <span data-ttu-id="78f2f-139">Azure Portal에서 앱을 실행 하기 전에 수동으로 blob을 만들거나 다음 절차를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-139">The blob can be manually created ahead of running the app in the Azure portal, or use the following procedure:</span></span>

1. <span data-ttu-id="78f2f-140">`ProtectKeysWithAzureKeyVault`첫 번째 실행에 대 한 호출을 제거 하 여 현재 위치의 blob을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-140">Remove the call to `ProtectKeysWithAzureKeyVault` for the first run to create the blob in place.</span></span>
1. <span data-ttu-id="78f2f-141">`ProtectKeysWithAzureKeyVault`후속 실행에 대 한 호출을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-141">Add the call to `ProtectKeysWithAzureKeyVault` for subsequent runs.</span></span>

<span data-ttu-id="78f2f-142">`ProtectKeysWithAzureKeyVault`적절 한 스키마 및 값을 사용 하 여 파일이 생성 되도록 하기 때문에 첫 번째 실행을 제거 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-142">Removing `ProtectKeysWithAzureKeyVault` for the first run is advised, as it ensures that the file is created with the proper schema and values in place.</span></span> 

<span data-ttu-id="78f2f-143">제공 된 API는 blob이 없는 경우 자동으로 만들기 때문에 [AspNetCore](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) 및 [AspNetCore](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) 패키지를 다시 업그레이드 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-143">We recommended upgrading to the [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) and [Azure.Extensions.AspNetCore.DataProtection.Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) packages because the API provided automatically creates the blob if it doesn't exist.</span></span>

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="78f2f-144">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="78f2f-144">PersistKeysToFileSystem</span></span>

<span data-ttu-id="78f2f-145">*% LOCALAPPDATA%* 기본 위치 대신 UNC 공유에 키를 저장 하려면 [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)을 사용 하 여 시스템을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-145">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="78f2f-146">키 지 속성 위치를 변경 하는 경우 DPAPI가 적절 한 암호화 메커니즘 인지 여부를 알 수 없으므로 시스템은 더 이상 휴지 상태의 키를 자동으로 암호화 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-146">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="78f2f-147">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="78f2f-147">ProtectKeysWith\*</span></span>

<span data-ttu-id="78f2f-148">[ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) 구성 api를 호출 하 여 미사용 키를 보호 하도록 시스템을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-148">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="78f2f-149">UNC 공유에 키를 저장 하 고 특정 x.509 인증서를 사용 하 여 미사용 키를 암호화 하는 아래 예제를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="78f2f-149">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78f2f-150">ASP.NET Core 2.1 이상에서는 파일에서 로드 한 인증서와 같은 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)에 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-150">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="78f2f-151">기본 제공 키 암호화 메커니즘에 대 한 자세한 예제 및 논의는 [미사용 키 암호화](xref:security/data-protection/implementation/key-encryption-at-rest) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="78f2f-151">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="78f2f-152">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="78f2f-152">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="78f2f-153">ASP.NET Core 2.1 이상에서는 [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)를 사용 하 여 [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 인증서 배열을 사용 하 여 rest에서 인증서를 회전 하 고 키를 해독할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-153">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="78f2f-154">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="78f2f-154">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="78f2f-155">기본 90 일 대신 14 일의 키 수명을 사용 하도록 시스템을 구성 하려면 [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-155">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="78f2f-156">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="78f2f-156">SetApplicationName</span></span>

<span data-ttu-id="78f2f-157">기본적으로 데이터 보호 시스템은 동일한 물리적 키 리포지토리를 공유 하는 경우에도 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 기반으로 하 여 앱을 서로 격리 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-157">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="78f2f-158">이렇게 하면 앱이 서로의 보호 된 페이로드를 이해할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-158">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="78f2f-159">앱 간에 보호 된 페이로드를 공유 하려면:</span><span class="sxs-lookup"><span data-stu-id="78f2f-159">To share protected payloads among apps:</span></span>

* <span data-ttu-id="78f2f-160"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>동일한 값을 사용 하 여 각 앱에서를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-160">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="78f2f-161">앱에서 동일한 버전의 데이터 보호 API 스택을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-161">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="78f2f-162">앱의 프로젝트 파일에서 다음 중 **하나** 를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-162">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="78f2f-163">[AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 통해 동일한 공유 프레임 워크 버전을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-163">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="78f2f-164">동일한 [데이터 보호 패키지](xref:security/data-protection/introduction#package-layout) 버전을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-164">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="78f2f-165">Disable자동 Keygeneration</span><span class="sxs-lookup"><span data-stu-id="78f2f-165">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="78f2f-166">앱에서 만료 방식으로 키를 자동으로 롤 롤 (새 키 생성) 하지 않으려는 시나리오가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-166">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="78f2f-167">이에 대 한 한 가지 예로 기본/보조 관계로 설정 된 앱이 있을 수 있습니다. 단, 기본 앱은 주요 관리 문제를 담당 하 고 보조 앱은 단순히 키 링의 읽기 전용 보기를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-167">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="78f2f-168">다음을 사용 하 여 시스템을 구성 하 여 키 링을 읽기 전용으로 처리 하도록 보조 앱을 구성할 수 있습니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> .</span><span class="sxs-lookup"><span data-stu-id="78f2f-168">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="78f2f-169">응용 프로그램별 격리</span><span class="sxs-lookup"><span data-stu-id="78f2f-169">Per-application isolation</span></span>

<span data-ttu-id="78f2f-170">ASP.NET Core 호스트에서 제공 하는 데이터 보호 시스템은 동일한 작업자 프로세스 계정에서 실행 되 고 동일한 마스터 키 자료를 사용 하는 경우에도 앱을 자동으로 격리 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-170">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="78f2f-171">이것은 System.web 요소의 IsolateApps 한정자와 약간 비슷합니다 `<machineKey>` .</span><span class="sxs-lookup"><span data-stu-id="78f2f-171">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="78f2f-172">격리 메커니즘은 로컬 컴퓨터의 각 앱을 고유 테 넌 트로 간주 하 여 작동 하므로 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 지정 된 앱에 대 한 루트에는 자동으로 앱 ID가 판별자로 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-172">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="78f2f-173">앱의 고유 ID는 앱의 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-173">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="78f2f-174">IIS에서 호스트 되는 앱의 경우 고유 ID는 앱의 IIS 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-174">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="78f2f-175">웹 팜 환경에 앱을 배포 하는 경우이 값은 안정적입니다. IIS 환경이 웹 팜의 모든 컴퓨터에서 유사 하 게 구성 되어 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-175">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="78f2f-176">[Kestrel 서버](xref:fundamentals/servers/index#kestrel)에서 실행 되는 자체 호스트 된 앱의 경우 고유 ID는 디스크의 앱에 대 한 실제 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-176">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="78f2f-177">고유 식별자는 &mdash; 개별 앱과 컴퓨터 자체를 모두 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-177">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="78f2f-178">이 격리 메커니즘에서는 앱이 악성이 아닌 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-178">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="78f2f-179">악의적인 앱은 항상 동일한 작업자 프로세스 계정에서 실행 되는 다른 앱에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-179">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="78f2f-180">응용 프로그램을 상호 신뢰할 수 없는 공유 호스팅 환경에서는 호스팅 공급자가 앱의 기본 키 리포지토리를 분리 하는 것을 포함 하 여 앱 간의 OS 수준 격리를 보장 하기 위한 단계를 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-180">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="78f2f-181">데이터 보호 시스템이 ASP.NET Core 호스트에서 제공 되지 않는 경우 (예: `DataProtectionProvider` 구체적 형식을 통해 인스턴스화하는 경우) 앱 격리가 기본적으로 사용 하지 않도록 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-181">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="78f2f-182">앱 격리가 사용 하지 않도록 설정 된 경우 동일한 키 자료로 지원 되는 모든 앱은 적절 한 [목적](xref:security/data-protection/consumer-apis/purpose-strings)을 제공 하기만 하면 페이로드를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-182">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="78f2f-183">이 환경에서 앱 격리를 제공 하려면 구성 개체에 대해 [Setapplicationname](#setapplicationname) 메서드를 호출 하 고 각 앱에 고유한 이름을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-183">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="78f2f-184">UseCryptographicAlgorithms를 사용 하 여 알고리즘 변경</span><span class="sxs-lookup"><span data-stu-id="78f2f-184">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="78f2f-185">데이터 보호 스택을 사용 하면 새로 생성 된 키에 사용 되는 기본 알고리즘을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-185">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="78f2f-186">이 작업을 수행 하는 가장 간단한 방법은 구성 콜백에서 [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) 를 호출 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-186">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="78f2f-187">기본 기본값은 AES-256-CBC이 고, 기본 ValidationAlgorithm은 HMACSHA256입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-187">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="78f2f-188">시스템 관리자는 시스템 수준 [정책을](xref:security/data-protection/configuration/machine-wide-policy)통해 기본 정책을 설정할 수 있지만에 대 한 명시적 호출은 `UseCryptographicAlgorithms` 기본 정책을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-188">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="78f2f-189">를 호출 하면 `UseCryptographicAlgorithms` 미리 정의 된 기본 제공 목록에서 원하는 알고리즘을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-189">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="78f2f-190">알고리즘의 구현에 대해 걱정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-190">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="78f2f-191">위의 시나리오에서 데이터 보호 시스템은 Windows에서 실행 되는 경우 AES의 CNG 구현을 사용 하려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-191">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="78f2f-192">그렇지 않은 경우에는 관리 되는 [system.web](/dotnet/api/system.security.cryptography.aes) 클래스를 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-192">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="78f2f-193">[UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)에 대 한 호출을 통해 구현을 수동으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-193">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="78f2f-194">알고리즘을 변경 해도 키 링의 기존 키에는 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-194">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="78f2f-195">새로 생성 된 키에만 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-195">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="78f2f-196">사용자 지정 관리 되는 알고리즘 지정</span><span class="sxs-lookup"><span data-stu-id="78f2f-196">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="78f2f-197">사용자 지정 관리 되는 알고리즘을 지정 하려면 구현 유형을 가리키는 [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-197">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="78f2f-198">사용자 지정 관리 되는 알고리즘을 지정 하려면 구현 유형을 가리키는 [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-198">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="78f2f-199">일반적으로 \* 형식 속성은 [System.security.cryptography.symmetricalgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) 및 [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)의 구체적이 고 인스턴스화할 수 있는 (매개 변수가 없는 public ctor를 통해) 구현을 가리켜야 합니다. 단, 시스템 특수 `typeof(Aes)` 한 경우에는 편의를 위해</span><span class="sxs-lookup"><span data-stu-id="78f2f-199">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="78f2f-200">System.security.cryptography.symmetricalgorithm의 키 길이는 ≥ 128 비트이 고 블록 크기는 ≥ 64 비트 여야 하 고, PKCS #7 패딩에서 CBC 모드 암호화를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-200">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="78f2f-201">KeyedHashAlgorithm는 >= 128 비트의 다이제스트 크기를 가져야 하며, 해시 알고리즘의 다이제스트 길이와 동일한 길이의 키를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-201">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="78f2f-202">KeyedHashAlgorithm는 HMAC가 반드시 필요한 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-202">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="78f2f-203">사용자 지정 Windows CNG 알고리즘 지정</span><span class="sxs-lookup"><span data-stu-id="78f2f-203">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="78f2f-204">HMAC 유효성 검사를 통해 CBC 모드 암호화를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-204">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="78f2f-205">HMAC 유효성 검사를 통해 CBC 모드 암호화를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-205">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="78f2f-206">대칭 블록 암호화 알고리즘의 키 길이는 >= 128 비트이 고, 블록 크기가 >= 64 비트이 고, PKCS #7 패딩을 사용 하는 CBC 모드 암호화를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-206">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="78f2f-207">해시 알고리즘의 다이제스트 크기는 >= 128 비트 여야 하며, BCRYPT \_ ALG \_ 핸들 \_ HMAC \_ 플래그 플래그를 사용 하 여 열 수 있도록 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-207">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="78f2f-208">\*지정 된 알고리즘에 대해 기본 공급자를 사용 하도록 공급자 속성을 null로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-208">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="78f2f-209">자세한 내용은 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="78f2f-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="78f2f-210">유효성 검사에 Galois/Counter 모드 암호화를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-210">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="78f2f-211">유효성 검사에 Galois/Counter 모드 암호화를 사용 하 여 사용자 지정 Windows CNG 알고리즘을 지정 하려면 알고리즘 정보를 포함 하는 [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-211">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="78f2f-212">대칭 블록 암호화 알고리즘의 키 길이는 >= 128 비트이 고, 블록 크기가 정확히 128 비트인 경우 GCM 암호화를 지원 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-212">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="78f2f-213">지정 된 알고리즘에 대해 기본 공급자를 사용 하도록 [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) 속성을 null로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-213">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="78f2f-214">자세한 내용은 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 설명서를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="78f2f-214">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="78f2f-215">기타 사용자 지정 알고리즘 지정</span><span class="sxs-lookup"><span data-stu-id="78f2f-215">Specifying other custom algorithms</span></span>

<span data-ttu-id="78f2f-216">데이터 보호 시스템은 최고 수준의 API로 노출 되지 않지만 거의 모든 종류의 알고리즘을 지정할 수 있을 만큼 확장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-216">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="78f2f-217">예를 들어 HSM (하드웨어 보안 모듈) 내에 포함 된 모든 키를 유지 하 고 핵심 암호화 및 암호 해독 루틴의 사용자 지정 구현을 제공 하는 것이 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-217">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="78f2f-218">자세한 내용은 [핵심 암호화 확장성](xref:security/data-protection/extensibility/core-crypto) 의 [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="78f2f-218">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="78f2f-219">Docker 컨테이너에서 호스팅할 때 키 유지</span><span class="sxs-lookup"><span data-stu-id="78f2f-219">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="78f2f-220">[Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) 컨테이너에서 호스트 하는 경우 다음과 같은 방법으로 키를 유지 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-220">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="78f2f-221">공유 볼륨 또는 호스트 탑재 볼륨과 같이 컨테이너 수명 이상으로 지속 되는 Docker 볼륨의 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-221">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="78f2f-222">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 또는 [Redis](https://redis.io/)와 같은 외부 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-222">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="78f2f-223">Redis를 사용 하 여 키 유지</span><span class="sxs-lookup"><span data-stu-id="78f2f-223">Persisting keys with Redis</span></span>

<span data-ttu-id="78f2f-224">[Redis 데이터 지 속성](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) 을 지 원하는 Redis 버전만 사용 하 여 키를 저장 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-224">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="78f2f-225">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) 는 영구적 이며 키를 저장 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f2f-225">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="78f2f-226">자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore/issues/13476)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78f2f-226">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78f2f-227">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="78f2f-227">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
