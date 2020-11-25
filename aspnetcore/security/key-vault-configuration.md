---
title: ASP.NET Core의 Azure Key Vault 구성 공급자
author: rick-anderson
description: Azure Key Vault 구성 공급자를 사용 하 여 런타임에 로드 된 이름-값 쌍을 사용 하 여 앱을 구성 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: 7f5cd3de38f1e45d9b188c513a0e62ca658b2992
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035907"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="f5cdc-103">ASP.NET Core의 Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="f5cdc-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="f5cdc-104">[Andrew Stanton-간호사](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="f5cdc-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f5cdc-105">이 문서에서는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용 하 여 Azure Key Vault 암호에서 앱 구성 값을 로드 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="f5cdc-106">Azure Key Vault는 앱 및 서비스에서 사용 하는 암호화 키 및 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="f5cdc-107">ASP.NET Core 앱과 함께 Azure Key Vault를 사용 하는 일반적인 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="f5cdc-108">중요 한 구성 데이터에 대 한 액세스 제어</span><span class="sxs-lookup"><span data-stu-id="f5cdc-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="f5cdc-109">구성 데이터를 저장할 때 FIPS 140-2 수준 2 유효성 검사 된 하드웨어 보안 모듈 (HSM)에 대 한 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="f5cdc-110">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5cdc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="f5cdc-111">패키지</span><span class="sxs-lookup"><span data-stu-id="f5cdc-111">Packages</span></span>

<span data-ttu-id="f5cdc-112">Azure.Extensions.AspNetCore.Configuration에 패키지 참조를 추가 [ 합니다. 비밀](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) 패키지.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-112">Add a package reference to the [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="f5cdc-113">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="f5cdc-113">Sample app</span></span>

<span data-ttu-id="f5cdc-114">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문에 의해 결정 되는 두 가지 모드 중 하나에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="f5cdc-115">`Certificate`: Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="f5cdc-116">이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="f5cdc-117">`Managed`: [Azure 리소스에 대해 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 앱의 코드 또는 구성에 저장 된 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="f5cdc-118">관리 id를 사용 하 여 인증 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="f5cdc-119">`Managed`샘플의 버전은 Azure에 배포 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="f5cdc-120">[Azure 리소스에 대 한 관리 되는 Id 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="f5cdc-121">전처리기 지시문 ()을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 `#define` 을 참조 하십시오 <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="f5cdc-122">개발 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="f5cdc-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="f5cdc-123">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 암호를 로컬로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f5cdc-124">샘플 앱이 개발 환경의 로컬 컴퓨터에서 실행 되는 경우 암호는 로컬 사용자 암호 저장소에서 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="f5cdc-125">비밀 관리자 도구에는 `<UserSecretsId>` 앱의 프로젝트 파일에 속성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="f5cdc-126">속성 값 ( `{GUID}` )을 고유한 GUID로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="f5cdc-127">비밀은 이름-값 쌍으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="f5cdc-128">계층 값 (구성 섹션) `:` [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름에 (콜론)을 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="f5cdc-129">암호 관리자는 프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 열린 명령 셸에서 사용 됩니다 `{SECRET NAME}` . 여기서은 이름이 고는 `{SECRET VALUE}` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="f5cdc-130">프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root) 에서 명령 셸에서 다음 명령을 실행 하 여 샘플 앱에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="f5cdc-131">이러한 암호가 Azure Key Vault 섹션을 사용 하 여 [프로덕션 환경의 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 Azure Key Vault에 저장 되는 경우 `_dev` 접미사가로 변경 됩니다 `_prod` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="f5cdc-132">접미사는 구성 값의 원본을 나타내는 응용 프로그램의 출력에 표시 되는 시각적 표시를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="f5cdc-133">Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장</span><span class="sxs-lookup"><span data-stu-id="f5cdc-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="f5cdc-134">[빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-cli) 항목은 샘플 앱에서 사용 하는 Azure Key Vault를 만들고 암호를 저장 하기 위해 여기에 요약 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="f5cdc-135">자세한 내용은 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="f5cdc-136">[Azure Portal](https://portal.azure.com/)에서 다음 방법 중 하나를 사용 하 여 Azure Cloud shell을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="f5cdc-137">코드 블록의 오른쪽 위 모서리에서 **사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="f5cdc-138">텍스트 상자에 "Azure CLI" 검색 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="f5cdc-139">**Cloud Shell 시작** 단추를 사용 하 여 브라우저에서 Cloud Shell를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="f5cdc-140">Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="f5cdc-141">자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="f5cdc-142">아직 인증 하지 않은 경우 명령을 사용 하 여 로그인 `az login` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="f5cdc-143">다음 명령을 사용 하 여 리소스 그룹을 만듭니다 `{RESOURCE GROUP NAME}` . 여기서는 새 리소스 그룹의 리소스 그룹 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f5cdc-144">다음 명령을 사용 하 여 리소스 그룹에 키 자격 증명 모음을 만듭니다 `{KEY VAULT NAME}` . 여기서은 새 키 자격 증명 모음에 대 한 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f5cdc-145">키 자격 증명 모음에서 암호를 이름-값 쌍으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="f5cdc-146">Azure Key Vault 비밀 이름은 영숫자 문자와 대시로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="f5cdc-147">계층 값 (구성 섹션) `--` 은 (대시 2 개)를 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="f5cdc-148">일반적으로 [ASP.NET Core 구성](xref:fundamentals/configuration/index)의 하위 키에서 섹션을 구분 하는 데 사용 되는 콜론은 key vault 암호 이름에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="f5cdc-149">따라서 비밀을 앱의 구성으로 로드할 때 콜론에 대해 두 개의 대시를 사용 하 고 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="f5cdc-150">다음 암호는 샘플 앱에서 사용 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="f5cdc-151">값에는 `_prod` `_dev` 사용자 암호에서 개발 환경에 로드 된 접미사 값과 구분 하는 접미사가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="f5cdc-152">`{KEY VAULT NAME}`을 이전 단계에서 만든 key vault의 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="f5cdc-153">Azure에서 호스트 되지 않는 앱에 응용 프로그램 ID 및 x.509 인증서 사용</span><span class="sxs-lookup"><span data-stu-id="f5cdc-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="f5cdc-154">**앱이 azure 외부에서 호스팅될 때** Azure Active Directory 응용 프로그램 ID 및 x.509 인증서를 사용 하 여 Key Vault에 인증 하도록 azure AD, Azure Key Vault 및 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="f5cdc-155">자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="f5cdc-156">Azure에서 호스트 되는 앱에는 응용 프로그램 ID 및 x.509 인증서를 사용할 수 있지만 azure에서 앱을 호스팅할 때 [azure 리소스에 관리 되는 id](#use-managed-identities-for-azure-resources) 를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="f5cdc-157">관리 되는 id는 응용 프로그램 또는 개발 환경에서 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="f5cdc-158">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 응용 프로그램 ID 및 x.509 인증서를 사용 `Certificate` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="f5cdc-159">PKCS # 12 archive (*.pfx*) 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="f5cdc-160">인증서를 만들기 위한 옵션에는 Windows 및 [OpenSSL](https://www.openssl.org/) [의 makecert.exe](/windows/desktop/seccrypto/makecert) 가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="f5cdc-161">현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="f5cdc-162">키를 내보낼 수 있는 것으로 표시는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="f5cdc-163">이 프로세스의 뒷부분에서 사용 되는 인증서의 지문을 적어둡니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="f5cdc-164">PKCS # 12 archive (*.pfx*) 인증서를 DER로 인코딩된 인증서 (*.cer*)로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="f5cdc-165">Azure AD (**앱 등록**)에 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="f5cdc-166">DER로 인코딩된 인증서 (*.cer*)를 Azure AD에 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="f5cdc-167">Azure AD에서 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="f5cdc-168">**인증서 & 암호** 로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="f5cdc-169">**인증서 업로드** 를 선택 하 여 공개 키가 포함 된 인증서를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="f5cdc-170">*.Cer*, *pem* 또는 *.crt* 인증서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="f5cdc-171">키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 파일에 저장 *appsettings.json* 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="f5cdc-172">Azure Portal에서 **키 자격 증명 모음** 으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="f5cdc-173">[Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 만든 key vault를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="f5cdc-174">**액세스 정책** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="f5cdc-175">**액세스 정책 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="f5cdc-176">**비밀 권한을** 열고 **Get** 및 **List** 권한을 사용 하 여 앱을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="f5cdc-177">**보안 주체 선택** 을 선택 하 고 이름으로 등록 된 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="f5cdc-178">**선택** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="f5cdc-179">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-179">Select **OK**.</span></span>
1. <span data-ttu-id="f5cdc-180">**저장** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-180">Select **Save**.</span></span>
1. <span data-ttu-id="f5cdc-181">앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-181">Deploy the app.</span></span>

<span data-ttu-id="f5cdc-182">`Certificate`샘플 앱은 `IConfigurationRoot` 암호 이름과 동일한 이름을 사용 하 여에서 해당 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="f5cdc-183">비 계층 구조 값:의 값은를 `SecretName` 사용 하 여 가져옵니다 `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="f5cdc-184">계층적 값 (섹션): `:` (콜론) 표기법 또는 `GetSection` 확장 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="f5cdc-185">다음 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="f5cdc-186">X.509 인증서는 OS를 통해 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="f5cdc-187">앱은 파일에서 제공한 값으로 **Addazurekeyvault** 를 호출 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-187">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="f5cdc-188">예제 값:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-188">Example values:</span></span>

* <span data-ttu-id="f5cdc-189">키 자격 증명 모음 이름: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="f5cdc-190">응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="f5cdc-191">인증서 지문: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="f5cdc-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="f5cdc-193">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f5cdc-194">개발 환경에서 비밀 값은 `_dev` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="f5cdc-195">프로덕션 환경에서 값은 `_prod` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="f5cdc-196">Azure 리소스에 관리 되는 id 사용</span><span class="sxs-lookup"><span data-stu-id="f5cdc-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="f5cdc-197">**Azure에 배포 된 앱** 은 앱이 앱에 저장 된 자격 증명 (응용 프로그램 ID 및 암호/클라이언트 암호) 없이 azure AD 인증을 사용 하 여 Azure Key Vault 인증할 수 있도록 하는 [azure 리소스에 대 한 관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="f5cdc-198">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 Azure 리소스에 대해 관리 id를 사용 `Managed` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="f5cdc-199">앱의 파일에 자격 증명 모음 이름을 입력 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="f5cdc-200">샘플 앱에는 버전으로 설정 된 경우 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 `Managed` 않으므로 이러한 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="f5cdc-201">앱이 Azure에 배포 되 고 Azure는 파일에 저장 된 자격 증명 모음 이름을 사용 하 여 Azure Key Vault에만 액세스 하도록 앱을 인증 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="f5cdc-202">Azure App Service에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="f5cdc-203">Azure App Service에 배포 된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="f5cdc-204">다음 명령에서 사용할 개체 ID를 배포에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="f5cdc-205">개체 ID는 App Service 패널의 Azure Portal에 표시 됩니다 **Identity** .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="f5cdc-206">Azure CLI 및 응용 프로그램의 개체 ID를 사용 하 여 앱에 `list` `get` 키 자격 증명 모음에 액세스할 수 있는 및 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="f5cdc-207">Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 **앱을 다시 시작** 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="f5cdc-208">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-208">The sample app:</span></span>

* <span data-ttu-id="f5cdc-209">클래스의 인스턴스를 만들고 `DefaultAzureCredential` , 자격 증명은 Azure 리소스에 대 한 환경에서 액세스 토큰을 가져오려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-209">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="f5cdc-210">새 [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) 이 인스턴스를 사용 하 여 만들어집니다 `DefaultAzureCredential` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-210">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="f5cdc-211">`Azure.Security.KeyVault.Secrets.Secrets`인스턴스는 `Azure.Extensions.Aspnetcore.Configuration.Secrets` 모든 비밀 값을 로드 하 고 `--` 키 이름에서 이중 대시 ()를 콜론 ()으로 대체 하는의 기본 구현과 함께 사용 됩니다 `:` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-211">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="f5cdc-212">Key vault 이름 예 값: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-212">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="f5cdc-213">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-213">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="f5cdc-214">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-214">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f5cdc-215">개발 환경에서 비밀 값은 `_dev` 사용자 비밀에서 제공 되므로 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-215">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="f5cdc-216">프로덕션 환경에서 값은 `_prod` Azure Key Vault에서 제공 되기 때문에 접미사를 사용 하 여 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-216">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="f5cdc-217">오류가 표시 되 면 `Access denied` 앱이 AZURE AD에 등록 되 고 키 자격 증명 모음에 대 한 액세스를 제공 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-217">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="f5cdc-218">Azure에서 서비스를 다시 시작 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-218">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="f5cdc-219">관리 되는 id 및 Azure DevOps 파이프라인에서 공급자를 사용 하는 방법에 대 한 자세한 내용은 [관리 서비스 id를 사용 하 여 VM에 대 한 Azure Resource Manager 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-219">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="f5cdc-220">구성 옵션</span><span class="sxs-lookup"><span data-stu-id="f5cdc-220">Configuration options</span></span>

<span data-ttu-id="f5cdc-221">AddAzureKeyVault는 AzureKeyVaultConfigurationOptions을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-221">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="f5cdc-222">속성</span><span class="sxs-lookup"><span data-stu-id="f5cdc-222">Property</span></span>         | <span data-ttu-id="f5cdc-223">Description</span><span class="sxs-lookup"><span data-stu-id="f5cdc-223">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="f5cdc-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` 비밀 로드를 제어 하는 데 사용 되는 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-224">`Azure.Extensions.Aspnetcore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="f5cdc-225">`Timespan` 변경에 대 한 주요 자격 증명 모음 폴링 시도 사이에 대기 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-225">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="f5cdc-226">기본값은 `null` (구성이 다시 로드 되지 않음)입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-226">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="f5cdc-227">키 이름 접두사 사용</span><span class="sxs-lookup"><span data-stu-id="f5cdc-227">Use a key name prefix</span></span>

<span data-ttu-id="f5cdc-228">AddAzureKeyVault는의 구현을 허용 하는 오버 로드를 제공 하며,이를 `Azure.Extensions.Aspnetcore.Configuration.Secrets` 통해 키 자격 증명 모음 비밀이 구성 키로 변환 되는 방식을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-228">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.Aspnetcore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="f5cdc-229">예를 들어, 인터페이스를 구현 하 여 앱 시작 시 제공 하는 접두사 값에 따라 비밀 값을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-229">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="f5cdc-230">예를 들어 앱의 버전에 따라 비밀을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-230">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="f5cdc-231">키 자격 증명 모음 비밀에 대 한 접두사를 사용 하 여 여러 앱에 대 한 비밀을 동일한 주요 자격 증명 모음에 저장 하거나 환경 비밀 (예: *개발* 및 *프로덕션* 비밀)을 동일한 자격 증명 모음에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-231">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="f5cdc-232">앱과 개발/프로덕션 환경 마다 별도의 키 자격 증명 모음을 사용 하 여 가장 높은 수준의 보안을 위해 앱 환경을 격리 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-232">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="f5cdc-233">다음 예제에서는 키 자격 증명 모음 (및 개발 환경에 대 한 암호 관리자 도구 사용)에 대 한 비밀이 설정 됩니다 `5000-AppSecret` (마침표는 key vault 암호 이름에 사용할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="f5cdc-233">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="f5cdc-234">이 암호는 앱의 버전 5.0.0.0 앱 암호를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-234">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="f5cdc-235">5.1.0.0 앱의 다른 버전에 대해서는에 대해 비밀 관리자 도구를 사용 하 여 키 자격 증명 모음에 암호를 추가 `5100-AppSecret` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-235">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="f5cdc-236">각 앱 버전은 해당 버전의 보안 값을 해당 구성으로 구성 하 `AppSecret` 고, 암호를 로드할 때 버전을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-236">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="f5cdc-237">AddAzureKeyVault는 사용자 지정을 사용 하 여 호출 됩니다 `Azure.Extensions.Aspnetcore.Configuration.Secrets` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-237">AddAzureKeyVault is called with a custom `Azure.Extensions.Aspnetcore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="f5cdc-238">`Azure.Extensions.Aspnetcore.Configuration.Secrets`구현은 암호의 버전 접두사에 반응 하 여 적절 한 비밀을 구성으로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-238">The `Azure.Extensions.Aspnetcore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="f5cdc-239">`Load` 이름이 접두사로 시작 하는 경우 비밀을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-239">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="f5cdc-240">다른 암호는 로드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-240">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="f5cdc-241">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-241">`GetKey`:</span></span>
  * <span data-ttu-id="f5cdc-242">비밀 이름에서 접두사를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-242">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="f5cdc-243">이름에 있는 두 대시 `KeyDelimiter` 를 구성에서 사용 되는 구분 기호인 (일반적으로 콜론)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-243">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="f5cdc-244">Azure Key Vault 암호 이름에 콜론을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-244">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="f5cdc-245">`Load`메서드는 버전 접두사가 있는 공급자를 찾기 위해 자격 증명 모음 암호를 반복 하는 공급자 알고리즘에 의해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-245">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="f5cdc-246">에 버전 접두사가 있으면 `Load` 알고리즘은 메서드를 사용 하 여 `GetKey` 비밀 이름의 구성 이름을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-246">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="f5cdc-247">암호 이름에서 버전 접두사를 제거 하 고 앱의 구성 이름-값 쌍으로 로드 하기 위한 비밀 이름의 나머지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-247">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="f5cdc-248">이 방법이 구현 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-248">When this approach is implemented:</span></span>

1. <span data-ttu-id="f5cdc-249">앱의 프로젝트 파일에 지정 된 앱 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-249">The app's version specified in the app's project file.</span></span> <span data-ttu-id="f5cdc-250">다음 예제에서 응용 프로그램의 버전은로 설정 됩니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-250">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f5cdc-251">`<UserSecretsId>`응용 프로그램의 프로젝트 파일에 속성이 있는지 확인 합니다 `{GUID}` . 여기서은 사용자가 제공한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-251">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="f5cdc-252">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 다음 암호를 로컬로 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-252">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="f5cdc-253">비밀은 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-253">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="f5cdc-254">앱이 실행 되 면 키 자격 증명 모음 비밀이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-254">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="f5cdc-255">에 대 한 문자열 비밀이 `5000-AppSecret` 응용 프로그램의 프로젝트 파일 ()에 지정 된 앱 버전과 일치 합니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-255">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="f5cdc-256">`5000`(대시 포함) 버전이 키 이름에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-256">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="f5cdc-257">앱 전체에서 키를 사용 하 여 구성을 읽으면 `AppSecret` 비밀 값이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-257">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="f5cdc-258">응용 프로그램의 버전이 프로젝트 파일에서로 변경 되 `5.1.0.0` 고 앱이 다시 실행 되는 경우 반환 되는 암호 값은 `5.1.0.0_secret_value_dev` 개발 환경 및 프로덕션 환경에 `5.1.0.0_secret_value_prod` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-258">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="f5cdc-259"><xref:Azure.Security.KeyVault.Secrets.SecretClient>AddAzureKeyVault에 대 한 고유한 구현을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-259">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="f5cdc-260">사용자 지정 클라이언트는 앱에서 클라이언트의 단일 인스턴스를 공유 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-260">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="f5cdc-261">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="f5cdc-261">Bind an array to a class</span></span>

<span data-ttu-id="f5cdc-262">공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-262">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="f5cdc-263">키에 콜론 () 구분 기호를 포함할 수 있는 구성 소스에서 읽을 때 `:` 숫자 키 세그먼트는 배열 ( `:0:` ,,)을 구성 하는 키를 구분 하는 데 사용 됩니다 `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-263">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="f5cdc-264">자세한 내용은 [구성: 클래스에 배열 바인딩](xref:fundamentals/configuration/index#bind-an-array-to-a-class)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-264">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="f5cdc-265">Azure Key Vault 키는 콜론을 구분 기호로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-265">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="f5cdc-266">이 항목에서 설명 하는 방법에서는 이중 대시 ( `--` )를 계층적 값 (섹션) 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-266">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="f5cdc-267">배열 키는 이중 대시 및 숫자 키 세그먼트 ( `--0--` ,,)를 사용 하는 Azure Key Vault에 저장 됩니다 `--1--` &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-267">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="f5cdc-268">JSON 파일에서 제공 하는 다음과 같은 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-268">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="f5cdc-269">`WriteTo`출력 로깅의 대상을 설명 하는 두 개의 Serilog *싱크* 를 반영 하는 두 개의 개체 리터럴이 배열에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-269">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="f5cdc-270">위의 JSON 파일에 표시 된 구성은 이중 대시 ( `--` ) 표기법 및 숫자 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-270">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="f5cdc-271">키</span><span class="sxs-lookup"><span data-stu-id="f5cdc-271">Key</span></span> | <span data-ttu-id="f5cdc-272">값</span><span class="sxs-lookup"><span data-stu-id="f5cdc-272">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="f5cdc-273">비밀 다시 로드</span><span class="sxs-lookup"><span data-stu-id="f5cdc-273">Reload secrets</span></span>

<span data-ttu-id="f5cdc-274">암호는가 호출 될 때까지 캐시 됩니다 `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-274">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="f5cdc-275">키 자격 증명 모음에서 만료, 사용 안 함 및 업데이트 된 비밀은가 실행 될 때까지 앱에서 적용 되지 않습니다 `Reload` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-275">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="f5cdc-276">비활성화 및 만료 된 비밀</span><span class="sxs-lookup"><span data-stu-id="f5cdc-276">Disabled and expired secrets</span></span>

<span data-ttu-id="f5cdc-277">비활성화 및 만료 된 비밀은을 throw <xref:Azure.RequestFailedException> 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-277">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="f5cdc-278">앱이 throw 되지 않도록 하려면 다른 구성 공급자를 사용 하 여 구성을 제공 하거나 사용 하지 않거나 만료 된 암호를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-278">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f5cdc-279">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f5cdc-279">Troubleshoot</span></span>

<span data-ttu-id="f5cdc-280">앱이 공급자를 사용 하 여 구성을 로드 하지 못하면 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)에 오류 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-280">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="f5cdc-281">다음 조건에서는 구성을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-281">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="f5cdc-282">앱 또는 인증서가 Azure Active Directory에서 올바르게 구성 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-282">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="f5cdc-283">키 자격 증명 모음이 Azure Key Vault에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-283">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="f5cdc-284">앱에 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-284">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="f5cdc-285">액세스 정책에는 `Get` 및 사용 권한이 포함 되어 있지 않습니다 `List` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-285">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="f5cdc-286">키 자격 증명 모음에서 구성 데이터 (이름-값 쌍)의 이름을 잘못 지정 했거나, 누락 되었거나, 사용 하지 않거나, 만료 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-286">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="f5cdc-287">앱에 잘못 된 키 자격 증명 모음 이름 ( `KeyVaultName` ), AZURE Ad 응용 프로그램 id () 또는 azure ad `AzureADApplicationId` 인증서 지문 () `AzureADCertThumbprint` 또는 Azure ad directoryid ( `AzureADDirectoryId` )가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-287">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="f5cdc-288">로드 하려는 값에 대 한 앱의 구성 키 (이름)가 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-288">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="f5cdc-289">키 자격 증명 모음에 앱에 대 한 액세스 정책을 추가 하는 경우 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추가 선택 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-289">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5cdc-290">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f5cdc-290">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="f5cdc-291">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="f5cdc-291">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="f5cdc-292">Microsoft Azure: Key Vault 설명서</span><span class="sxs-lookup"><span data-stu-id="f5cdc-292">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="f5cdc-293">Azure Key Vault 용 HSM 보호 키를 생성 하 고 전송 하는 방법</span><span class="sxs-lookup"><span data-stu-id="f5cdc-293">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="f5cdc-294">KeyVaultClient 클래스</span><span class="sxs-lookup"><span data-stu-id="f5cdc-294">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="f5cdc-295">빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색</span><span class="sxs-lookup"><span data-stu-id="f5cdc-295">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="f5cdc-296">자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="f5cdc-296">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f5cdc-297">이 문서에서는 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용 하 여 Azure Key Vault 암호에서 앱 구성 값을 로드 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-297">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="f5cdc-298">Azure Key Vault는 앱 및 서비스에서 사용 하는 암호화 키 및 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-298">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="f5cdc-299">ASP.NET Core 앱과 함께 Azure Key Vault를 사용 하는 일반적인 시나리오는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-299">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="f5cdc-300">중요 한 구성 데이터에 대 한 액세스 제어</span><span class="sxs-lookup"><span data-stu-id="f5cdc-300">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="f5cdc-301">구성 데이터를 저장할 때 FIPS 140-2 수준 2 유효성 검사 된 하드웨어 보안 모듈 (HSM)에 대 한 요구 사항을 충족 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-301">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="f5cdc-302">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f5cdc-302">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="f5cdc-303">패키지</span><span class="sxs-lookup"><span data-stu-id="f5cdc-303">Packages</span></span>

<span data-ttu-id="f5cdc-304">Microsoft.Extensions.Configuration에 패키지 참조를 추가 [ 합니다. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-304">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="f5cdc-305">샘플 앱</span><span class="sxs-lookup"><span data-stu-id="f5cdc-305">Sample app</span></span>

<span data-ttu-id="f5cdc-306">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문에 의해 결정 되는 두 가지 모드 중 하나에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-306">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="f5cdc-307">`Certificate`: Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-307">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="f5cdc-308">이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-308">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="f5cdc-309">`Managed`: [Azure 리소스에 대해 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 앱의 코드 또는 구성에 저장 된 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-309">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="f5cdc-310">관리 id를 사용 하 여 인증 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-310">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="f5cdc-311">`Managed`샘플의 버전은 Azure에 배포 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-311">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="f5cdc-312">[Azure 리소스에 대 한 관리 되는 Id 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-312">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="f5cdc-313">전처리기 지시문 ()을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 `#define` 을 참조 하십시오 <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-313">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="f5cdc-314">개발 환경의 비밀 저장소</span><span class="sxs-lookup"><span data-stu-id="f5cdc-314">Secret storage in the Development environment</span></span>

<span data-ttu-id="f5cdc-315">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 암호를 로컬로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-315">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f5cdc-316">샘플 앱이 개발 환경의 로컬 컴퓨터에서 실행 되는 경우 암호는 로컬 사용자 암호 저장소에서 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-316">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="f5cdc-317">비밀 관리자 도구에는 `<UserSecretsId>` 앱의 프로젝트 파일에 속성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-317">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="f5cdc-318">속성 값 ( `{GUID}` )을 고유한 GUID로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-318">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="f5cdc-319">비밀은 이름-값 쌍으로 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-319">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="f5cdc-320">계층 값 (구성 섹션) `:` [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름에 (콜론)을 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-320">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="f5cdc-321">암호 관리자는 프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 열린 명령 셸에서 사용 됩니다 `{SECRET NAME}` . 여기서은 이름이 고는 `{SECRET VALUE}` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-321">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="f5cdc-322">프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root) 에서 명령 셸에서 다음 명령을 실행 하 여 샘플 앱에 대 한 암호를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-322">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="f5cdc-323">이러한 암호가 Azure Key Vault 섹션을 사용 하 여 [프로덕션 환경의 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 Azure Key Vault에 저장 되는 경우 `_dev` 접미사가로 변경 됩니다 `_prod` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-323">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="f5cdc-324">접미사는 구성 값의 원본을 나타내는 응용 프로그램의 출력에 표시 되는 시각적 표시를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-324">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="f5cdc-325">Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장</span><span class="sxs-lookup"><span data-stu-id="f5cdc-325">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="f5cdc-326">[빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-cli) 항목은 샘플 앱에서 사용 하는 Azure Key Vault를 만들고 암호를 저장 하기 위해 여기에 요약 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-326">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="f5cdc-327">자세한 내용은 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-327">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="f5cdc-328">[Azure Portal](https://portal.azure.com/)에서 다음 방법 중 하나를 사용 하 여 Azure Cloud shell을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-328">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="f5cdc-329">코드 블록의 오른쪽 위 모서리에서 **사용** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-329">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="f5cdc-330">텍스트 상자에 "Azure CLI" 검색 문자열을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-330">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="f5cdc-331">**Cloud Shell 시작** 단추를 사용 하 여 브라우저에서 Cloud Shell를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-331">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="f5cdc-332">Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-332">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="f5cdc-333">자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-333">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="f5cdc-334">아직 인증 하지 않은 경우 명령을 사용 하 여 로그인 `az login` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-334">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="f5cdc-335">다음 명령을 사용 하 여 리소스 그룹을 만듭니다 `{RESOURCE GROUP NAME}` . 여기서는 새 리소스 그룹의 리소스 그룹 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-335">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f5cdc-336">다음 명령을 사용 하 여 리소스 그룹에 키 자격 증명 모음을 만듭니다 `{KEY VAULT NAME}` . 여기서은 새 키 자격 증명 모음에 대 한 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-336">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="f5cdc-337">키 자격 증명 모음에서 암호를 이름-값 쌍으로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-337">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="f5cdc-338">Azure Key Vault 비밀 이름은 영숫자 문자와 대시로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-338">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="f5cdc-339">계층 값 (구성 섹션) `--` 은 (대시 2 개)를 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-339">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="f5cdc-340">일반적으로 [ASP.NET Core 구성](xref:fundamentals/configuration/index)의 하위 키에서 섹션을 구분 하는 데 사용 되는 콜론은 key vault 암호 이름에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-340">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="f5cdc-341">따라서 비밀을 앱의 구성으로 로드할 때 콜론에 대해 두 개의 대시를 사용 하 고 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-341">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="f5cdc-342">다음 암호는 샘플 앱에서 사용 하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-342">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="f5cdc-343">값에는 `_prod` `_dev` 사용자 암호에서 개발 환경에 로드 된 접미사 값과 구분 하는 접미사가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-343">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="f5cdc-344">`{KEY VAULT NAME}`을 이전 단계에서 만든 key vault의 이름으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-344">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="f5cdc-345">Azure에서 호스트 되지 않는 앱에 응용 프로그램 ID 및 x.509 인증서 사용</span><span class="sxs-lookup"><span data-stu-id="f5cdc-345">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="f5cdc-346">**앱이 azure 외부에서 호스팅될 때** Azure Active Directory 응용 프로그램 ID 및 x.509 인증서를 사용 하 여 Key Vault에 인증 하도록 azure AD, Azure Key Vault 및 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-346">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="f5cdc-347">자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-347">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="f5cdc-348">Azure에서 호스트 되는 앱에는 응용 프로그램 ID 및 x.509 인증서를 사용할 수 있지만 azure에서 앱을 호스팅할 때 [azure 리소스에 관리 되는 id](#use-managed-identities-for-azure-resources) 를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-348">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="f5cdc-349">관리 되는 id는 응용 프로그램 또는 개발 환경에서 인증서를 저장할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-349">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="f5cdc-350">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 응용 프로그램 ID 및 x.509 인증서를 사용 `Certificate` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-350">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="f5cdc-351">PKCS # 12 archive (*.pfx*) 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-351">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="f5cdc-352">인증서를 만들기 위한 옵션에는 Windows 및 [OpenSSL](https://www.openssl.org/) [의 makecert.exe](/windows/desktop/seccrypto/makecert) 가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-352">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="f5cdc-353">현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-353">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="f5cdc-354">키를 내보낼 수 있는 것으로 표시는 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-354">Marking the key as exportable is optional.</span></span> <span data-ttu-id="f5cdc-355">이 프로세스의 뒷부분에서 사용 되는 인증서의 지문을 적어둡니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-355">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="f5cdc-356">PKCS # 12 archive (*.pfx*) 인증서를 DER로 인코딩된 인증서 (*.cer*)로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-356">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="f5cdc-357">Azure AD (**앱 등록**)에 앱을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-357">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="f5cdc-358">DER로 인코딩된 인증서 (*.cer*)를 Azure AD에 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-358">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="f5cdc-359">Azure AD에서 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-359">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="f5cdc-360">**인증서 & 암호** 로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-360">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="f5cdc-361">**인증서 업로드** 를 선택 하 여 공개 키가 포함 된 인증서를 업로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-361">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="f5cdc-362">*.Cer*, *pem* 또는 *.crt* 인증서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-362">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="f5cdc-363">키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 파일에 저장 *appsettings.json* 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-363">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="f5cdc-364">Azure Portal에서 **키 자격 증명 모음** 으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-364">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="f5cdc-365">[Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 만든 key vault를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-365">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="f5cdc-366">**액세스 정책** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-366">Select **Access policies**.</span></span>
1. <span data-ttu-id="f5cdc-367">**액세스 정책 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-367">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="f5cdc-368">**비밀 권한을** 열고 **Get** 및 **List** 권한을 사용 하 여 앱을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-368">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="f5cdc-369">**보안 주체 선택** 을 선택 하 고 이름으로 등록 된 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-369">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="f5cdc-370">**선택** 단추를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-370">Select the **Select** button.</span></span>
1. <span data-ttu-id="f5cdc-371">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-371">Select **OK**.</span></span>
1. <span data-ttu-id="f5cdc-372">**저장** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-372">Select **Save**.</span></span>
1. <span data-ttu-id="f5cdc-373">앱을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-373">Deploy the app.</span></span>

<span data-ttu-id="f5cdc-374">`Certificate`샘플 앱은 `IConfigurationRoot` 암호 이름과 동일한 이름을 사용 하 여에서 해당 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-374">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="f5cdc-375">비 계층 구조 값:의 값은를 `SecretName` 사용 하 여 가져옵니다 `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-375">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="f5cdc-376">계층적 값 (섹션): `:` (콜론) 표기법 또는 `GetSection` 확장 메서드를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-376">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="f5cdc-377">다음 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-377">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="f5cdc-378">X.509 인증서는 OS를 통해 관리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-378">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="f5cdc-379">응용 프로그램은 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 파일에서 제공 하는 값을 사용 하 여를 호출 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-379">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="f5cdc-380">예제 값:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-380">Example values:</span></span>

* <span data-ttu-id="f5cdc-381">키 자격 증명 모음 이름: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-381">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="f5cdc-382">응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-382">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="f5cdc-383">인증서 지문: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-383">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="f5cdc-384">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-384">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="f5cdc-385">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-385">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f5cdc-386">개발 환경에서 비밀 값은 `_dev` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-386">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="f5cdc-387">프로덕션 환경에서 값은 `_prod` 접미사로 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-387">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="f5cdc-388">Azure 리소스에 관리 되는 id 사용</span><span class="sxs-lookup"><span data-stu-id="f5cdc-388">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="f5cdc-389">**Azure에 배포 된 앱** 은 앱이 앱에 저장 된 자격 증명 (응용 프로그램 ID 및 암호/클라이언트 암호) 없이 azure AD 인증을 사용 하 여 Azure Key Vault 인증할 수 있도록 하는 [azure 리소스에 대 한 관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-389">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="f5cdc-390">샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 Azure 리소스에 대해 관리 id를 사용 `Managed` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-390">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="f5cdc-391">앱의 파일에 자격 증명 모음 이름을 입력 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-391">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="f5cdc-392">샘플 앱에는 버전으로 설정 된 경우 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 `Managed` 않으므로 이러한 구성 항목을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-392">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="f5cdc-393">앱이 Azure에 배포 되 고 Azure는 파일에 저장 된 자격 증명 모음 이름을 사용 하 여 Azure Key Vault에만 액세스 하도록 앱을 인증 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-393">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="f5cdc-394">Azure App Service에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-394">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="f5cdc-395">Azure App Service에 배포 된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-395">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="f5cdc-396">다음 명령에서 사용할 개체 ID를 배포에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-396">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="f5cdc-397">개체 ID는 App Service 패널의 Azure Portal에 표시 됩니다 **Identity** .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-397">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="f5cdc-398">Azure CLI 및 응용 프로그램의 개체 ID를 사용 하 여 앱에 `list` `get` 키 자격 증명 모음에 액세스할 수 있는 및 권한을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-398">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="f5cdc-399">Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 **앱을 다시 시작** 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-399">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="f5cdc-400">샘플 앱:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-400">The sample app:</span></span>

* <span data-ttu-id="f5cdc-401">`AzureServiceTokenProvider`연결 문자열을 사용 하지 않고 클래스의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-401">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="f5cdc-402">연결 문자열이 제공 되지 않으면 공급자는 Azure 리소스에 대 한 관리 되는 id에서 액세스 토큰을 가져오려고 시도 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-402">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="f5cdc-403"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스 토큰 콜백을 사용 하 여 새을 만듭니다 `AzureServiceTokenProvider` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-403">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="f5cdc-404"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스는 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 모든 비밀 값을 로드 하 고 `--` 키 이름에서 이중 대시 ()를 콜론 ()으로 대체 하는의 기본 구현과 함께 사용 됩니다 `:` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-404">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="f5cdc-405">Key vault 이름 예 값: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="f5cdc-405">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="f5cdc-406">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-406">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="f5cdc-407">앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-407">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="f5cdc-408">개발 환경에서 비밀 값은 `_dev` 사용자 비밀에서 제공 되므로 접미사가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-408">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="f5cdc-409">프로덕션 환경에서 값은 `_prod` Azure Key Vault에서 제공 되기 때문에 접미사를 사용 하 여 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-409">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="f5cdc-410">오류가 표시 되 면 `Access denied` 앱이 AZURE AD에 등록 되 고 키 자격 증명 모음에 대 한 액세스를 제공 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-410">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="f5cdc-411">Azure에서 서비스를 다시 시작 했는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-411">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="f5cdc-412">관리 되는 id 및 Azure DevOps 파이프라인에서 공급자를 사용 하는 방법에 대 한 자세한 내용은 [관리 서비스 id를 사용 하 여 VM에 대 한 Azure Resource Manager 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-412">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="f5cdc-413">키 이름 접두사 사용</span><span class="sxs-lookup"><span data-stu-id="f5cdc-413">Use a key name prefix</span></span>

<span data-ttu-id="f5cdc-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 는의 구현을 허용 하는 오버 로드를 제공 하 여 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 키 자격 증명 모음 비밀이 구성 키로 변환 되는 방식을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-414"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="f5cdc-415">예를 들어, 인터페이스를 구현 하 여 앱 시작 시 제공 하는 접두사 값에 따라 비밀 값을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-415">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="f5cdc-416">예를 들어 앱의 버전에 따라 비밀을 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-416">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="f5cdc-417">키 자격 증명 모음 비밀에 대 한 접두사를 사용 하 여 여러 앱에 대 한 비밀을 동일한 주요 자격 증명 모음에 저장 하거나 환경 비밀 (예: *개발* 및 *프로덕션* 비밀)을 동일한 자격 증명 모음에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-417">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="f5cdc-418">앱과 개발/프로덕션 환경 마다 별도의 키 자격 증명 모음을 사용 하 여 가장 높은 수준의 보안을 위해 앱 환경을 격리 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-418">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="f5cdc-419">다음 예제에서는 키 자격 증명 모음 (및 개발 환경에 대 한 암호 관리자 도구 사용)에 대 한 비밀이 설정 됩니다 `5000-AppSecret` (마침표는 key vault 암호 이름에 사용할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="f5cdc-419">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="f5cdc-420">이 암호는 앱의 버전 5.0.0.0 앱 암호를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-420">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="f5cdc-421">5.1.0.0 앱의 다른 버전에 대해서는에 대해 비밀 관리자 도구를 사용 하 여 키 자격 증명 모음에 암호를 추가 `5100-AppSecret` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-421">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="f5cdc-422">각 앱 버전은 해당 버전의 보안 값을 해당 구성으로 구성 하 `AppSecret` 고, 암호를 로드할 때 버전을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-422">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="f5cdc-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 사용자 지정을 사용 하 여 호출 됩니다 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-423"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="f5cdc-424"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>구현은 암호의 버전 접두사에 반응 하 여 적절 한 비밀을 구성으로 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-424">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="f5cdc-425">`Load` 이름이 접두사로 시작 하는 경우 비밀을 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-425">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="f5cdc-426">다른 암호는 로드 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-426">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="f5cdc-427">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-427">`GetKey`:</span></span>
  * <span data-ttu-id="f5cdc-428">비밀 이름에서 접두사를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-428">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="f5cdc-429">이름에 있는 두 대시 `KeyDelimiter` 를 구성에서 사용 되는 구분 기호인 (일반적으로 콜론)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-429">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="f5cdc-430">Azure Key Vault 암호 이름에 콜론을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-430">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="f5cdc-431">`Load`메서드는 버전 접두사가 있는 공급자를 찾기 위해 자격 증명 모음 암호를 반복 하는 공급자 알고리즘에 의해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-431">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="f5cdc-432">에 버전 접두사가 있으면 `Load` 알고리즘은 메서드를 사용 하 여 `GetKey` 비밀 이름의 구성 이름을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-432">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="f5cdc-433">암호 이름에서 버전 접두사를 제거 하 고 앱의 구성 이름-값 쌍으로 로드 하기 위한 비밀 이름의 나머지를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-433">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="f5cdc-434">이 방법이 구현 되는 경우:</span><span class="sxs-lookup"><span data-stu-id="f5cdc-434">When this approach is implemented:</span></span>

1. <span data-ttu-id="f5cdc-435">앱의 프로젝트 파일에 지정 된 앱 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-435">The app's version specified in the app's project file.</span></span> <span data-ttu-id="f5cdc-436">다음 예제에서 응용 프로그램의 버전은로 설정 됩니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-436">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f5cdc-437">`<UserSecretsId>`응용 프로그램의 프로젝트 파일에 속성이 있는지 확인 합니다 `{GUID}` . 여기서은 사용자가 제공한 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-437">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="f5cdc-438">[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 다음 암호를 로컬로 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-438">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="f5cdc-439">비밀은 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-439">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="f5cdc-440">앱이 실행 되 면 키 자격 증명 모음 비밀이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-440">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="f5cdc-441">에 대 한 문자열 비밀이 `5000-AppSecret` 응용 프로그램의 프로젝트 파일 ()에 지정 된 앱 버전과 일치 합니다 `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-441">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="f5cdc-442">`5000`(대시 포함) 버전이 키 이름에서 제거 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-442">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="f5cdc-443">앱 전체에서 키를 사용 하 여 구성을 읽으면 `AppSecret` 비밀 값이 로드 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-443">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="f5cdc-444">응용 프로그램의 버전이 프로젝트 파일에서로 변경 되 `5.1.0.0` 고 앱이 다시 실행 되는 경우 반환 되는 암호 값은 `5.1.0.0_secret_value_dev` 개발 환경 및 프로덕션 환경에 `5.1.0.0_secret_value_prod` 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-444">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="f5cdc-445">사용자 고유의 구현을 제공할 수도 있습니다 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-445">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="f5cdc-446">사용자 지정 클라이언트는 앱에서 클라이언트의 단일 인스턴스를 공유 하도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-446">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="f5cdc-447">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="f5cdc-447">Bind an array to a class</span></span>

<span data-ttu-id="f5cdc-448">공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-448">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="f5cdc-449">키에 콜론 () 구분 기호를 포함할 수 있는 구성 소스에서 읽을 때 `:` 숫자 키 세그먼트는 배열 ( `:0:` ,,)을 구성 하는 키를 구분 하는 데 사용 됩니다 `:1:` &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-449">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="f5cdc-450">자세한 내용은 [구성: 클래스에 배열 바인딩](xref:fundamentals/configuration/index#bind-an-array-to-a-class)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-450">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="f5cdc-451">Azure Key Vault 키는 콜론을 구분 기호로 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-451">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="f5cdc-452">이 항목에서 설명 하는 방법에서는 이중 대시 ( `--` )를 계층적 값 (섹션) 구분 기호로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-452">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="f5cdc-453">배열 키는 이중 대시 및 숫자 키 세그먼트 ( `--0--` ,,)를 사용 하는 Azure Key Vault에 저장 됩니다 `--1--` &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-453">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="f5cdc-454">JSON 파일에서 제공 하는 다음과 같은 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검토 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-454">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="f5cdc-455">`WriteTo`출력 로깅의 대상을 설명 하는 두 개의 Serilog *싱크* 를 반영 하는 두 개의 개체 리터럴이 배열에 정의 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-455">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="f5cdc-456">위의 JSON 파일에 표시 된 구성은 이중 대시 ( `--` ) 표기법 및 숫자 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-456">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="f5cdc-457">키</span><span class="sxs-lookup"><span data-stu-id="f5cdc-457">Key</span></span> | <span data-ttu-id="f5cdc-458">값</span><span class="sxs-lookup"><span data-stu-id="f5cdc-458">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="f5cdc-459">비밀 다시 로드</span><span class="sxs-lookup"><span data-stu-id="f5cdc-459">Reload secrets</span></span>

<span data-ttu-id="f5cdc-460">암호는가 호출 될 때까지 캐시 됩니다 `IConfigurationRoot.Reload()` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-460">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="f5cdc-461">키 자격 증명 모음에서 만료, 사용 안 함 및 업데이트 된 비밀은가 실행 될 때까지 앱에서 적용 되지 않습니다 `Reload` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-461">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="f5cdc-462">비활성화 및 만료 된 비밀</span><span class="sxs-lookup"><span data-stu-id="f5cdc-462">Disabled and expired secrets</span></span>

<span data-ttu-id="f5cdc-463">비활성화 및 만료 된 비밀은을 throw <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-463">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="f5cdc-464">앱이 throw 되지 않도록 하려면 다른 구성 공급자를 사용 하 여 구성을 제공 하거나 사용 하지 않거나 만료 된 암호를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-464">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f5cdc-465">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f5cdc-465">Troubleshoot</span></span>

<span data-ttu-id="f5cdc-466">앱이 공급자를 사용 하 여 구성을 로드 하지 못하면 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)에 오류 메시지가 기록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-466">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="f5cdc-467">다음 조건에서는 구성을 로드할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-467">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="f5cdc-468">앱 또는 인증서가 Azure Active Directory에서 올바르게 구성 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-468">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="f5cdc-469">키 자격 증명 모음이 Azure Key Vault에 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-469">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="f5cdc-470">앱에 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-470">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="f5cdc-471">액세스 정책에는 `Get` 및 사용 권한이 포함 되어 있지 않습니다 `List` .</span><span class="sxs-lookup"><span data-stu-id="f5cdc-471">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="f5cdc-472">키 자격 증명 모음에서 구성 데이터 (이름-값 쌍)의 이름을 잘못 지정 했거나, 누락 되었거나, 사용 하지 않거나, 만료 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-472">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="f5cdc-473">앱에 잘못 된 키 자격 증명 모음 이름 ( `KeyVaultName` ), AZURE Ad 응용 프로그램 Id () `AzureADApplicationId` 또는 azure ad 인증서 지문 ( `AzureADCertThumbprint` )이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-473">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="f5cdc-474">로드 하려는 값에 대 한 앱의 구성 키 (이름)가 잘못 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-474">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="f5cdc-475">키 자격 증명 모음에 앱에 대 한 액세스 정책을 추가 하는 경우 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추가 선택 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="f5cdc-475">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5cdc-476">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f5cdc-476">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="f5cdc-477">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="f5cdc-477">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="f5cdc-478">Microsoft Azure: Key Vault 설명서</span><span class="sxs-lookup"><span data-stu-id="f5cdc-478">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="f5cdc-479">Azure Key Vault 용 HSM 보호 키를 생성 하 고 전송 하는 방법</span><span class="sxs-lookup"><span data-stu-id="f5cdc-479">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="f5cdc-480">KeyVaultClient 클래스</span><span class="sxs-lookup"><span data-stu-id="f5cdc-480">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="f5cdc-481">빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색</span><span class="sxs-lookup"><span data-stu-id="f5cdc-481">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="f5cdc-482">자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="f5cdc-482">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
