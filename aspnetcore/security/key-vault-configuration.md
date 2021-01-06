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
ms.openlocfilehash: 4b035fe59b8576eb387ddce67943386ccab55492
ms.sourcegitcommit: 8dfcd2b4be936950c228b4d98430622a04254cd7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/26/2020
ms.locfileid: "97792085"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core의 Azure Key Vault 구성 공급자

[Andrew Stanton-간호사](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

이 문서에서는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용 하 여 Azure Key Vault 암호에서 앱 구성 값을 로드 하는 방법을 설명 합니다. Azure Key Vault는 앱 및 서비스에서 사용 하는 암호화 키 및 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다. ASP.NET Core 앱과 함께 Azure Key Vault를 사용 하는 일반적인 시나리오는 다음과 같습니다.

* 중요 한 구성 데이터에 대 한 액세스 제어
* 구성 데이터를 저장할 때 FIPS 140-2 수준 2 유효성 검사 된 하드웨어 보안 모듈 (HSM)에 대 한 요구 사항을 충족 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="packages"></a>패키지

다음 패키지에 대 한 패키지 참조를 추가 합니다.

* [Azure.Extensions.AspNetCore.Configuration.Secrets](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* [Microsoft.Identity](https://www.nuget.org/packages/Azure.Identity)

## <a name="sample-app"></a>샘플 앱

샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문에 의해 결정 되는 두 가지 모드 중 하나에서 실행 됩니다.

* `Certificate`: Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다. 이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.
* `Managed`: [Azure 리소스에 대해 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 앱의 코드 또는 구성에 저장 된 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다. 관리 id를 사용 하 여 인증 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않습니다. `Managed`샘플의 버전은 Azure에 배포 되어야 합니다. [Azure 리소스에 대 한 관리 되는 Id 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.

전처리기 지시문 ()을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 `#define` 을 참조 하십시오 <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>개발 환경의 비밀 저장소

[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 암호를 로컬로 설정 합니다. 샘플 앱이 개발 환경의 로컬 컴퓨터에서 실행 되는 경우 암호는 로컬 사용자 암호 저장소에서 로드 됩니다.

비밀 관리자 도구에는 `<UserSecretsId>` 앱의 프로젝트 파일에 속성이 필요 합니다. 속성 값 ( `{GUID}` )을 고유한 GUID로 설정 합니다.

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

비밀은 이름-값 쌍으로 생성 됩니다. 계층 값 (구성 섹션) `:` [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름에 (콜론)을 구분 기호로 사용 합니다.

암호 관리자는 프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 열린 명령 셸에서 사용 됩니다 `{SECRET NAME}` . 여기서은 이름이 고는 `{SECRET VALUE}` 값입니다.

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root) 에서 명령 셸에서 다음 명령을 실행 하 여 샘플 앱에 대 한 암호를 설정 합니다.

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

이러한 암호가 Azure Key Vault 섹션을 사용 하 여 [프로덕션 환경의 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 Azure Key Vault에 저장 되는 경우 `_dev` 접미사가로 변경 됩니다 `_prod` . 접미사는 구성 값의 원본을 나타내는 응용 프로그램의 출력에 표시 되는 시각적 표시를 제공 합니다.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장

[빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-cli) 항목은 샘플 앱에서 사용 하는 Azure Key Vault를 만들고 암호를 저장 하기 위해 여기에 요약 되어 있습니다. 자세한 내용은 항목을 참조 하세요.

1. [Azure Portal](https://portal.azure.com/)에서 다음 방법 중 하나를 사용 하 여 Azure Cloud shell을 엽니다.

   * 코드 블록의 오른쪽 위 모서리에서 **사용** 을 선택합니다. 텍스트 상자에 "Azure CLI" 검색 문자열을 사용 합니다.
   * **Cloud Shell 시작** 단추를 사용 하 여 브라우저에서 Cloud Shell를 엽니다.
   * Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.

   자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)를 참조 하세요.

1. 아직 인증 하지 않은 경우 명령을 사용 하 여 로그인 `az login` 합니다.

1. 다음 명령을 사용 하 여 리소스 그룹을 만듭니다 `{RESOURCE GROUP NAME}` . 여기서는 새 리소스 그룹의 리소스 그룹 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 다음 명령을 사용 하 여 리소스 그룹에 키 자격 증명 모음을 만듭니다 `{KEY VAULT NAME}` . 여기서은 새 키 자격 증명 모음에 대 한 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 키 자격 증명 모음에서 암호를 이름-값 쌍으로 만듭니다.

   Azure Key Vault 비밀 이름은 영숫자 문자와 대시로 제한 됩니다. 계층 값 (구성 섹션) `--` 은 (대시 2 개)를 구분 기호로 사용 합니다. 일반적으로 [ASP.NET Core 구성](xref:fundamentals/configuration/index)의 하위 키에서 섹션을 구분 하는 데 사용 되는 콜론은 key vault 암호 이름에 사용할 수 없습니다. 따라서 비밀을 앱의 구성으로 로드할 때 콜론에 대해 두 개의 대시를 사용 하 고 대체 합니다.

   다음 암호는 샘플 앱에서 사용 하기 위한 것입니다. 값에는 `_prod` `_dev` 사용자 암호에서 개발 환경에 로드 된 접미사 값과 구분 하는 접미사가 포함 됩니다. `{KEY VAULT NAME}`을 이전 단계에서 만든 key vault의 이름으로 바꿉니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure에서 호스트 되지 않는 앱에 응용 프로그램 ID 및 x.509 인증서 사용

**앱이 azure 외부에서 호스팅될 때** Azure Active Directory 응용 프로그램 ID 및 x.509 인증서를 사용 하 여 Key Vault에 인증 하도록 azure AD, Azure Key Vault 및 앱을 구성 합니다. 자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.

> [!NOTE]
> Azure에서 호스트 되는 앱에는 응용 프로그램 ID 및 x.509 인증서를 사용할 수 있지만 azure에서 앱을 호스팅할 때 [azure 리소스에 관리 되는 id](#use-managed-identities-for-azure-resources) 를 사용 하는 것이 좋습니다. 관리 되는 id는 응용 프로그램 또는 개발 환경에서 인증서를 저장할 필요가 없습니다.

샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 응용 프로그램 ID 및 x.509 인증서를 사용 `Certificate` 합니다.

1. PKCS # 12 archive (*.pfx*) 인증서를 만듭니다. 인증서를 만들기 위한 옵션에는 Windows 및 [OpenSSL](https://www.openssl.org/) [의 makecert.exe](/windows/desktop/seccrypto/makecert) 가 포함 됩니다.
1. 현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다. 키를 내보낼 수 있는 것으로 표시는 선택 사항입니다. 이 프로세스의 뒷부분에서 사용 되는 인증서의 지문을 적어둡니다.
1. PKCS # 12 archive (*.pfx*) 인증서를 DER로 인코딩된 인증서 (*.cer*)로 내보냅니다.
1. Azure AD (**앱 등록**)에 앱을 등록 합니다.
1. DER로 인코딩된 인증서 (*.cer*)를 Azure AD에 업로드 합니다.
   1. Azure AD에서 앱을 선택 합니다.
   1. **인증서 & 암호** 로 이동 합니다.
   1. **인증서 업로드** 를 선택 하 여 공개 키가 포함 된 인증서를 업로드 합니다. *.Cer*, *pem* 또는 *.crt* 인증서를 사용할 수 있습니다.
1. 키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 파일에 저장 *appsettings.json* 합니다.
1. Azure Portal에서 **키 자격 증명 모음** 으로 이동 합니다.
1. [Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 만든 key vault를 선택 합니다.
1. **액세스 정책** 을 선택합니다.
1. **액세스 정책 추가** 를 선택합니다.
1. **비밀 권한을** 열고 **Get** 및 **List** 권한을 사용 하 여 앱을 제공 합니다.
1. **보안 주체 선택** 을 선택 하 고 이름으로 등록 된 앱을 선택 합니다. **선택** 단추를 누릅니다.
1. **확인** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 앱을 배포합니다.

`Certificate`샘플 앱은 `IConfigurationRoot` 암호 이름과 동일한 이름을 사용 하 여에서 해당 구성 값을 가져옵니다.

* 비 계층 구조 값:의 값은를 `SecretName` 사용 하 여 가져옵니다 `config["SecretName"]` .
* 계층적 값 (섹션): `:` (콜론) 표기법 또는 `GetSection` 확장 메서드를 사용 합니다. 다음 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 인증서는 OS를 통해 관리 됩니다. 앱은 파일에서 제공한 값으로 **Addazurekeyvault** 를 호출 합니다 *appsettings.json* .

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

예제 값:

* 키 자격 증명 모음 이름: `contosovault`
* 응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 인증서 지문: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다. 개발 환경에서 비밀 값은 `_dev` 접미사로 로드 됩니다. 프로덕션 환경에서 값은 `_prod` 접미사로 로드 됩니다.

## <a name="use-managed-identities-for-azure-resources"></a>Azure 리소스에 관리 되는 id 사용

**Azure에 배포 된 앱** 은 앱이 앱에 저장 된 자격 증명 (응용 프로그램 ID 및 암호/클라이언트 암호) 없이 azure AD 인증을 사용 하 여 Azure Key Vault 인증할 수 있도록 하는 [azure 리소스에 대 한 관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 활용할 수 있습니다.

샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 Azure 리소스에 대해 관리 id를 사용 `Managed` 합니다.

앱의 파일에 자격 증명 모음 이름을 입력 합니다 *appsettings.json* . 샘플 앱에는 버전으로 설정 된 경우 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 `Managed` 않으므로 이러한 구성 항목을 무시할 수 있습니다. 앱이 Azure에 배포 되 고 Azure는 파일에 저장 된 자격 증명 모음 이름을 사용 하 여 Azure Key Vault에만 액세스 하도록 앱을 인증 합니다 *appsettings.json* .

Azure App Service에 샘플 앱을 배포 합니다.

Azure App Service에 배포 된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록 됩니다. 다음 명령에서 사용할 개체 ID를 배포에서 가져옵니다. 개체 ID는 App Service 패널의 Azure Portal에 표시 됩니다 **Identity** .

Azure CLI 및 응용 프로그램의 개체 ID를 사용 하 여 앱에 `list` `get` 키 자격 증명 모음에 액세스할 수 있는 및 권한을 제공 합니다.

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 **앱을 다시 시작** 합니다.

샘플 앱:

* 클래스의 인스턴스를 만들고 `DefaultAzureCredential` , 자격 증명은 Azure 리소스에 대 한 환경에서 액세스 토큰을 가져오려고 시도 합니다.
* 새 [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) 이 인스턴스를 사용 하 여 만들어집니다 `DefaultAzureCredential` .
* `Azure.Security.KeyVault.Secrets.Secrets`인스턴스는 `Azure.Extensions.AspNetCore.Configuration.Secrets` 모든 비밀 값을 로드 하 고 `--` 키 이름에서 이중 대시 ()를 콜론 ()으로 대체 하는의 기본 구현과 함께 사용 됩니다 `:` .

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

Key vault 이름 예 값: `contosovault`

*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다. 개발 환경에서 비밀 값은 `_dev` 사용자 비밀에서 제공 되므로 접미사가 있습니다. 프로덕션 환경에서 값은 `_prod` Azure Key Vault에서 제공 되기 때문에 접미사를 사용 하 여 로드 됩니다.

오류가 표시 되 면 `Access denied` 앱이 AZURE AD에 등록 되 고 키 자격 증명 모음에 대 한 액세스를 제공 했는지 확인 합니다. Azure에서 서비스를 다시 시작 했는지 확인 합니다.

관리 되는 id 및 Azure DevOps 파이프라인에서 공급자를 사용 하는 방법에 대 한 자세한 내용은 [관리 서비스 id를 사용 하 여 VM에 대 한 Azure Resource Manager 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)를 참조 하세요.

## <a name="configuration-options"></a>구성 옵션

AddAzureKeyVault는 AzureKeyVaultConfigurationOptions을 허용할 수 있습니다.

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| 속성         | 설명 |
| ---------------- | ----------- |
| `Manager`        | `Azure.Extensions.AspNetCore.Configuration.Secrets` 비밀 로드를 제어 하는 데 사용 되는 인스턴스입니다. |
| `ReloadInterval` | `Timespan` 변경에 대 한 주요 자격 증명 모음 폴링 시도 사이에 대기 합니다. 기본값은 `null` (구성이 다시 로드 되지 않음)입니다. |

## <a name="use-a-key-name-prefix"></a>키 이름 접두사 사용

AddAzureKeyVault는의 구현을 허용 하는 오버 로드를 제공 하며,이를 `Azure.Extensions.AspNetCore.Configuration.Secrets` 통해 키 자격 증명 모음 비밀이 구성 키로 변환 되는 방식을 제어할 수 있습니다. 예를 들어, 인터페이스를 구현 하 여 앱 시작 시 제공 하는 접두사 값에 따라 비밀 값을 로드할 수 있습니다. 예를 들어 앱의 버전에 따라 비밀을 로드할 수 있습니다.

> [!WARNING]
> 키 자격 증명 모음 비밀에 대 한 접두사를 사용 하 여 여러 앱에 대 한 비밀을 동일한 주요 자격 증명 모음에 저장 하거나 환경 비밀 (예: *개발* 및 *프로덕션* 비밀)을 동일한 자격 증명 모음에 저장 하지 마세요. 앱과 개발/프로덕션 환경 마다 별도의 키 자격 증명 모음을 사용 하 여 가장 높은 수준의 보안을 위해 앱 환경을 격리 하는 것이 좋습니다.

다음 예제에서는 키 자격 증명 모음 (및 개발 환경에 대 한 암호 관리자 도구 사용)에 대 한 비밀이 설정 됩니다 `5000-AppSecret` (마침표는 key vault 암호 이름에 사용할 수 없음). 이 암호는 앱의 버전 5.0.0.0 앱 암호를 나타냅니다. 5.1.0.0 앱의 다른 버전에 대해서는에 대해 비밀 관리자 도구를 사용 하 여 키 자격 증명 모음에 암호를 추가 `5100-AppSecret` 합니다. 각 앱 버전은 해당 버전의 보안 값을 해당 구성으로 구성 하 `AppSecret` 고, 암호를 로드할 때 버전을 제거 합니다.

AddAzureKeyVault는 사용자 지정을 사용 하 여 호출 됩니다 `Azure.Extensions.AspNetCore.Configuration.Secrets` .

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

`Azure.Extensions.AspNetCore.Configuration.Secrets`구현은 암호의 버전 접두사에 반응 하 여 적절 한 비밀을 구성으로 로드 합니다.

* `Load` 이름이 접두사로 시작 하는 경우 비밀을 로드 합니다. 다른 암호는 로드 되지 않습니다.
* `GetKey`:
  * 비밀 이름에서 접두사를 제거 합니다.
  * 이름에 있는 두 대시 `KeyDelimiter` 를 구성에서 사용 되는 구분 기호인 (일반적으로 콜론)로 바꿉니다. Azure Key Vault 암호 이름에 콜론을 허용 하지 않습니다.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`메서드는 버전 접두사가 있는 공급자를 찾기 위해 자격 증명 모음 암호를 반복 하는 공급자 알고리즘에 의해 호출 됩니다. 에 버전 접두사가 있으면 `Load` 알고리즘은 메서드를 사용 하 여 `GetKey` 비밀 이름의 구성 이름을 반환 합니다. 암호 이름에서 버전 접두사를 제거 하 고 앱의 구성 이름-값 쌍으로 로드 하기 위한 비밀 이름의 나머지를 반환 합니다.

이 방법이 구현 되는 경우:

1. 앱의 프로젝트 파일에 지정 된 앱 버전입니다. 다음 예제에서 응용 프로그램의 버전은로 설정 됩니다 `5.0.0.0` .

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`응용 프로그램의 프로젝트 파일에 속성이 있는지 확인 합니다 `{GUID}` . 여기서은 사용자가 제공한 GUID입니다.

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 다음 암호를 로컬로 저장 합니다.

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 비밀은 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 앱이 실행 되 면 키 자격 증명 모음 비밀이 로드 됩니다. 에 대 한 문자열 비밀이 `5000-AppSecret` 응용 프로그램의 프로젝트 파일 ()에 지정 된 앱 버전과 일치 합니다 `5.0.0.0` .

1. `5000`(대시 포함) 버전이 키 이름에서 제거 됩니다. 앱 전체에서 키를 사용 하 여 구성을 읽으면 `AppSecret` 비밀 값이 로드 됩니다.

1. 응용 프로그램의 버전이 프로젝트 파일에서로 변경 되 `5.1.0.0` 고 앱이 다시 실행 되는 경우 반환 되는 암호 값은 `5.1.0.0_secret_value_dev` 개발 환경 및 프로덕션 환경에 `5.1.0.0_secret_value_prod` 있습니다.

> [!NOTE]
> <xref:Azure.Security.KeyVault.Secrets.SecretClient>AddAzureKeyVault에 대 한 고유한 구현을 제공할 수도 있습니다. 사용자 지정 클라이언트는 앱에서 클라이언트의 단일 인스턴스를 공유 하도록 허용 합니다.

## <a name="bind-an-array-to-a-class"></a>클래스에 배열 바인딩

공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.

키에 콜론 () 구분 기호를 포함할 수 있는 구성 소스에서 읽을 때 `:` 숫자 키 세그먼트는 배열 ( `:0:` ,,)을 구성 하는 키를 구분 하는 데 사용 됩니다 `:1:` &hellip; `:{n}:` . 자세한 내용은 [구성: 클래스에 배열 바인딩](xref:fundamentals/configuration/index#bind-an-array-to-a-class)을 참조 하세요.

Azure Key Vault 키는 콜론을 구분 기호로 사용할 수 없습니다. 이 항목에서 설명 하는 방법에서는 이중 대시 ( `--` )를 계층적 값 (섹션) 구분 기호로 사용 합니다. 배열 키는 이중 대시 및 숫자 키 세그먼트 ( `--0--` ,,)를 사용 하는 Azure Key Vault에 저장 됩니다 `--1--` &hellip; `--{n}--` .

JSON 파일에서 제공 하는 다음과 같은 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검토 합니다. `WriteTo`출력 로깅의 대상을 설명 하는 두 개의 Serilog *싱크* 를 반영 하는 두 개의 개체 리터럴이 배열에 정의 되어 있습니다.

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

위의 JSON 파일에 표시 된 구성은 이중 대시 ( `--` ) 표기법 및 숫자 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다.

| 키 | 값 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>비밀 다시 로드

암호는가 호출 될 때까지 캐시 됩니다 `IConfigurationRoot.Reload()` . 키 자격 증명 모음에서 만료, 사용 안 함 및 업데이트 된 비밀은가 실행 될 때까지 앱에서 적용 되지 않습니다 `Reload` .

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>비활성화 및 만료 된 비밀

비활성화 및 만료 된 비밀은을 throw <xref:Azure.RequestFailedException> 합니다. 앱이 throw 되지 않도록 하려면 다른 구성 공급자를 사용 하 여 구성을 제공 하거나 사용 하지 않거나 만료 된 암호를 업데이트 합니다.

## <a name="troubleshoot"></a>문제 해결

앱이 공급자를 사용 하 여 구성을 로드 하지 못하면 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)에 오류 메시지가 기록 됩니다. 다음 조건에서는 구성을 로드할 수 없습니다.

* 앱 또는 인증서가 Azure Active Directory에서 올바르게 구성 되지 않았습니다.
* 키 자격 증명 모음이 Azure Key Vault에 없습니다.
* 앱에 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.
* 액세스 정책에는 `Get` 및 사용 권한이 포함 되어 있지 않습니다 `List` .
* 키 자격 증명 모음에서 구성 데이터 (이름-값 쌍)의 이름을 잘못 지정 했거나, 누락 되었거나, 사용 하지 않거나, 만료 되었습니다.
* 앱에 잘못 된 키 자격 증명 모음 이름 ( `KeyVaultName` ), AZURE Ad 응용 프로그램 id () 또는 azure ad `AzureADApplicationId` 인증서 지문 () `AzureADCertThumbprint` 또는 Azure ad directoryid ( `AzureADDirectoryId` )가 있습니다.
* 로드 하려는 값에 대 한 앱의 구성 키 (이름)가 잘못 되었습니다.
* 키 자격 증명 모음에 앱에 대 한 액세스 정책을 추가 하는 경우 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추가 선택 되지 않았습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault 설명서](/azure/key-vault/)
* [Azure Key Vault 용 HSM 보호 키를 생성 하 고 전송 하는 방법](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 클래스](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-net)
* [자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

이 문서에서는 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 구성 공급자를 사용 하 여 Azure Key Vault 암호에서 앱 구성 값을 로드 하는 방법을 설명 합니다. Azure Key Vault는 앱 및 서비스에서 사용 하는 암호화 키 및 암호를 보호 하는 데 도움이 되는 클라우드 기반 서비스입니다. ASP.NET Core 앱과 함께 Azure Key Vault를 사용 하는 일반적인 시나리오는 다음과 같습니다.

* 중요 한 구성 데이터에 대 한 액세스 제어
* 구성 데이터를 저장할 때 FIPS 140-2 수준 2 유효성 검사 된 하드웨어 보안 모듈 (HSM)에 대 한 요구 사항을 충족 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="packages"></a>패키지

Microsoft.Extensions.Configuration에 패키지 참조를 추가 [ 합니다. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 패키지입니다.

## <a name="sample-app"></a>샘플 앱

샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문에 의해 결정 되는 두 가지 모드 중 하나에서 실행 됩니다.

* `Certificate`: Azure Key Vault 클라이언트 ID 및 x.509 인증서를 사용 하 여 Azure Key Vault에 저장 된 암호에 액세스 하는 방법을 보여 줍니다. 이 버전의 샘플은 Azure App Service 또는 ASP.NET Core 앱을 제공할 수 있는 호스트에 배포 된 모든 위치에서 실행할 수 있습니다.
* `Managed`: [Azure 리소스에 대해 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 앱의 코드 또는 구성에 저장 된 자격 증명 없이 azure AD 인증을 사용 하 여 Azure Key Vault 하도록 앱을 인증 하는 방법을 보여 줍니다. 관리 id를 사용 하 여 인증 하는 경우 Azure AD 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 않습니다. `Managed`샘플의 버전은 Azure에 배포 되어야 합니다. [Azure 리소스에 대 한 관리 되는 Id 사용](#use-managed-identities-for-azure-resources) 섹션의 지침을 따릅니다.

전처리기 지시문 ()을 사용 하 여 샘플 앱을 구성 하는 방법에 대 한 자세한 내용은 `#define` 을 참조 하십시오 <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>개발 환경의 비밀 저장소

[암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 암호를 로컬로 설정 합니다. 샘플 앱이 개발 환경의 로컬 컴퓨터에서 실행 되는 경우 암호는 로컬 사용자 암호 저장소에서 로드 됩니다.

비밀 관리자 도구에는 `<UserSecretsId>` 앱의 프로젝트 파일에 속성이 필요 합니다. 속성 값 ( `{GUID}` )을 고유한 GUID로 설정 합니다.

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

비밀은 이름-값 쌍으로 생성 됩니다. 계층 값 (구성 섹션) `:` [ASP.NET Core 구성](xref:fundamentals/configuration/index) 키 이름에 (콜론)을 구분 기호로 사용 합니다.

암호 관리자는 프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root)에 열린 명령 셸에서 사용 됩니다 `{SECRET NAME}` . 여기서은 이름이 고는 `{SECRET VALUE}` 값입니다.

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

프로젝트의 [콘텐츠 루트](xref:fundamentals/index#content-root) 에서 명령 셸에서 다음 명령을 실행 하 여 샘플 앱에 대 한 암호를 설정 합니다.

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

이러한 암호가 Azure Key Vault 섹션을 사용 하 여 [프로덕션 환경의 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 Azure Key Vault에 저장 되는 경우 `_dev` 접미사가로 변경 됩니다 `_prod` . 접미사는 구성 값의 원본을 나타내는 응용 프로그램의 출력에 표시 되는 시각적 표시를 제공 합니다.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장

[빠른 시작: Azure CLI를 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-cli) 항목은 샘플 앱에서 사용 하는 Azure Key Vault를 만들고 암호를 저장 하기 위해 여기에 요약 되어 있습니다. 자세한 내용은 항목을 참조 하세요.

1. [Azure Portal](https://portal.azure.com/)에서 다음 방법 중 하나를 사용 하 여 Azure Cloud shell을 엽니다.

   * 코드 블록의 오른쪽 위 모서리에서 **사용** 을 선택합니다. 텍스트 상자에 "Azure CLI" 검색 문자열을 사용 합니다.
   * **Cloud Shell 시작** 단추를 사용 하 여 브라우저에서 Cloud Shell를 엽니다.
   * Azure Portal의 오른쪽 위 모서리에 있는 메뉴에서 **Cloud Shell** 단추를 선택합니다.

   자세한 내용은 [Azure CLI](/cli/azure/) 및 [Azure Cloud Shell 개요](/azure/cloud-shell/overview)를 참조 하세요.

1. 아직 인증 하지 않은 경우 명령을 사용 하 여 로그인 `az login` 합니다.

1. 다음 명령을 사용 하 여 리소스 그룹을 만듭니다 `{RESOURCE GROUP NAME}` . 여기서는 새 리소스 그룹의 리소스 그룹 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 다음 명령을 사용 하 여 리소스 그룹에 키 자격 증명 모음을 만듭니다 `{KEY VAULT NAME}` . 여기서은 새 키 자격 증명 모음에 대 한 이름이 고 `{LOCATION}` 은 Azure 지역 (데이터 센터)입니다.

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 키 자격 증명 모음에서 암호를 이름-값 쌍으로 만듭니다.

   Azure Key Vault 비밀 이름은 영숫자 문자와 대시로 제한 됩니다. 계층 값 (구성 섹션) `--` 은 (대시 2 개)를 구분 기호로 사용 합니다. 일반적으로 [ASP.NET Core 구성](xref:fundamentals/configuration/index)의 하위 키에서 섹션을 구분 하는 데 사용 되는 콜론은 key vault 암호 이름에 사용할 수 없습니다. 따라서 비밀을 앱의 구성으로 로드할 때 콜론에 대해 두 개의 대시를 사용 하 고 대체 합니다.

   다음 암호는 샘플 앱에서 사용 하기 위한 것입니다. 값에는 `_prod` `_dev` 사용자 암호에서 개발 환경에 로드 된 접미사 값과 구분 하는 접미사가 포함 됩니다. `{KEY VAULT NAME}`을 이전 단계에서 만든 key vault의 이름으로 바꿉니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure에서 호스트 되지 않는 앱에 응용 프로그램 ID 및 x.509 인증서 사용

**앱이 azure 외부에서 호스팅될 때** Azure Active Directory 응용 프로그램 ID 및 x.509 인증서를 사용 하 여 Key Vault에 인증 하도록 azure AD, Azure Key Vault 및 앱을 구성 합니다. 자세한 내용은 [키, 암호 및 인증서 정보](/azure/key-vault/about-keys-secrets-and-certificates)를 참조하세요.

> [!NOTE]
> Azure에서 호스트 되는 앱에는 응용 프로그램 ID 및 x.509 인증서를 사용할 수 있지만 azure에서 앱을 호스팅할 때 [azure 리소스에 관리 되는 id](#use-managed-identities-for-azure-resources) 를 사용 하는 것이 좋습니다. 관리 되는 id는 응용 프로그램 또는 개발 환경에서 인증서를 저장할 필요가 없습니다.

샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 응용 프로그램 ID 및 x.509 인증서를 사용 `Certificate` 합니다.

1. PKCS # 12 archive (*.pfx*) 인증서를 만듭니다. 인증서를 만들기 위한 옵션에는 Windows 및 [OpenSSL](https://www.openssl.org/) [의 makecert.exe](/windows/desktop/seccrypto/makecert) 가 포함 됩니다.
1. 현재 사용자의 개인 인증서 저장소에 인증서를 설치 합니다. 키를 내보낼 수 있는 것으로 표시는 선택 사항입니다. 이 프로세스의 뒷부분에서 사용 되는 인증서의 지문을 적어둡니다.
1. PKCS # 12 archive (*.pfx*) 인증서를 DER로 인코딩된 인증서 (*.cer*)로 내보냅니다.
1. Azure AD (**앱 등록**)에 앱을 등록 합니다.
1. DER로 인코딩된 인증서 (*.cer*)를 Azure AD에 업로드 합니다.
   1. Azure AD에서 앱을 선택 합니다.
   1. **인증서 & 암호** 로 이동 합니다.
   1. **인증서 업로드** 를 선택 하 여 공개 키가 포함 된 인증서를 업로드 합니다. *.Cer*, *pem* 또는 *.crt* 인증서를 사용할 수 있습니다.
1. 키 자격 증명 모음 이름, 응용 프로그램 ID 및 인증서 지문을 앱의 파일에 저장 *appsettings.json* 합니다.
1. Azure Portal에서 **키 자격 증명 모음** 으로 이동 합니다.
1. [Azure Key Vault를 사용 하 여 프로덕션 환경에서 암호 저장소](#secret-storage-in-the-production-environment-with-azure-key-vault) 에 만든 key vault를 선택 합니다.
1. **액세스 정책** 을 선택합니다.
1. **액세스 정책 추가** 를 선택합니다.
1. **비밀 권한을** 열고 **Get** 및 **List** 권한을 사용 하 여 앱을 제공 합니다.
1. **보안 주체 선택** 을 선택 하 고 이름으로 등록 된 앱을 선택 합니다. **선택** 단추를 누릅니다.
1. **확인** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 앱을 배포합니다.

`Certificate`샘플 앱은 `IConfigurationRoot` 암호 이름과 동일한 이름을 사용 하 여에서 해당 구성 값을 가져옵니다.

* 비 계층 구조 값:의 값은를 `SecretName` 사용 하 여 가져옵니다 `config["SecretName"]` .
* 계층적 값 (섹션): `:` (콜론) 표기법 또는 `GetSection` 확장 메서드를 사용 합니다. 다음 방법 중 하나를 사용 하 여 구성 값을 가져옵니다.
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 인증서는 OS를 통해 관리 됩니다. 응용 프로그램은 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 파일에서 제공 하는 값을 사용 하 여를 호출 합니다 *appsettings.json* .

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

예제 값:

* 키 자격 증명 모음 이름: `contosovault`
* 응용 프로그램 ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 인증서 지문: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다. 개발 환경에서 비밀 값은 `_dev` 접미사로 로드 됩니다. 프로덕션 환경에서 값은 `_prod` 접미사로 로드 됩니다.

## <a name="use-managed-identities-for-azure-resources"></a>Azure 리소스에 관리 되는 id 사용

**Azure에 배포 된 앱** 은 앱이 앱에 저장 된 자격 증명 (응용 프로그램 ID 및 암호/클라이언트 암호) 없이 azure AD 인증을 사용 하 여 Azure Key Vault 인증할 수 있도록 하는 [azure 리소스에 대 한 관리 id](/azure/active-directory/managed-identities-azure-resources/overview)를 활용할 수 있습니다.

샘플 앱은 `#define` *Program.cs* 파일의 맨 위에 있는 문이로 설정 된 경우 Azure 리소스에 대해 관리 id를 사용 `Managed` 합니다.

앱의 파일에 자격 증명 모음 이름을 입력 합니다 *appsettings.json* . 샘플 앱에는 버전으로 설정 된 경우 응용 프로그램 ID 및 암호 (클라이언트 암호)가 필요 하지 `Managed` 않으므로 이러한 구성 항목을 무시할 수 있습니다. 앱이 Azure에 배포 되 고 Azure는 파일에 저장 된 자격 증명 모음 이름을 사용 하 여 Azure Key Vault에만 액세스 하도록 앱을 인증 합니다 *appsettings.json* .

Azure App Service에 샘플 앱을 배포 합니다.

Azure App Service에 배포 된 앱은 서비스를 만들 때 Azure AD에 자동으로 등록 됩니다. 다음 명령에서 사용할 개체 ID를 배포에서 가져옵니다. 개체 ID는 App Service 패널의 Azure Portal에 표시 됩니다 **Identity** .

Azure CLI 및 응용 프로그램의 개체 ID를 사용 하 여 앱에 `list` `get` 키 자격 증명 모음에 액세스할 수 있는 및 권한을 제공 합니다.

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI, PowerShell 또는 Azure Portal를 사용 하 여 **앱을 다시 시작** 합니다.

샘플 앱:

* `AzureServiceTokenProvider`연결 문자열을 사용 하지 않고 클래스의 인스턴스를 만듭니다. 연결 문자열이 제공 되지 않으면 공급자는 Azure 리소스에 대 한 관리 되는 id에서 액세스 토큰을 가져오려고 시도 합니다.
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스 토큰 콜백을 사용 하 여 새을 만듭니다 `AzureServiceTokenProvider` .
* <xref:Microsoft.Azure.KeyVault.KeyVaultClient>인스턴스는 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 모든 비밀 값을 로드 하 고 `--` 키 이름에서 이중 대시 ()를 콜론 ()으로 대체 하는의 기본 구현과 함께 사용 됩니다 `:` .

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Key vault 이름 예 값: `contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

앱을 실행 하면 웹 페이지에 로드 된 비밀 값이 표시 됩니다. 개발 환경에서 비밀 값은 `_dev` 사용자 비밀에서 제공 되므로 접미사가 있습니다. 프로덕션 환경에서 값은 `_prod` Azure Key Vault에서 제공 되기 때문에 접미사를 사용 하 여 로드 됩니다.

오류가 표시 되 면 `Access denied` 앱이 AZURE AD에 등록 되 고 키 자격 증명 모음에 대 한 액세스를 제공 했는지 확인 합니다. Azure에서 서비스를 다시 시작 했는지 확인 합니다.

관리 되는 id 및 Azure DevOps 파이프라인에서 공급자를 사용 하는 방법에 대 한 자세한 내용은 [관리 서비스 id를 사용 하 여 VM에 대 한 Azure Resource Manager 서비스 연결 만들기](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)를 참조 하세요.

## <a name="use-a-key-name-prefix"></a>키 이름 접두사 사용

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 는의 구현을 허용 하는 오버 로드를 제공 하 여 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 키 자격 증명 모음 비밀이 구성 키로 변환 되는 방식을 제어할 수 있습니다. 예를 들어, 인터페이스를 구현 하 여 앱 시작 시 제공 하는 접두사 값에 따라 비밀 값을 로드할 수 있습니다. 예를 들어 앱의 버전에 따라 비밀을 로드할 수 있습니다.

> [!WARNING]
> 키 자격 증명 모음 비밀에 대 한 접두사를 사용 하 여 여러 앱에 대 한 비밀을 동일한 주요 자격 증명 모음에 저장 하거나 환경 비밀 (예: *개발* 및 *프로덕션* 비밀)을 동일한 자격 증명 모음에 저장 하지 마세요. 앱과 개발/프로덕션 환경 마다 별도의 키 자격 증명 모음을 사용 하 여 가장 높은 수준의 보안을 위해 앱 환경을 격리 하는 것이 좋습니다.

다음 예제에서는 키 자격 증명 모음 (및 개발 환경에 대 한 암호 관리자 도구 사용)에 대 한 비밀이 설정 됩니다 `5000-AppSecret` (마침표는 key vault 암호 이름에 사용할 수 없음). 이 암호는 앱의 버전 5.0.0.0 앱 암호를 나타냅니다. 5.1.0.0 앱의 다른 버전에 대해서는에 대해 비밀 관리자 도구를 사용 하 여 키 자격 증명 모음에 암호를 추가 `5100-AppSecret` 합니다. 각 앱 버전은 해당 버전의 보안 값을 해당 구성으로 구성 하 `AppSecret` 고, 암호를 로드할 때 버전을 제거 합니다.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 사용자 지정을 사용 하 여 호출 됩니다 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> .

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>구현은 암호의 버전 접두사에 반응 하 여 적절 한 비밀을 구성으로 로드 합니다.

* `Load` 이름이 접두사로 시작 하는 경우 비밀을 로드 합니다. 다른 암호는 로드 되지 않습니다.
* `GetKey`:
  * 비밀 이름에서 접두사를 제거 합니다.
  * 이름에 있는 두 대시 `KeyDelimiter` 를 구성에서 사용 되는 구분 기호인 (일반적으로 콜론)로 바꿉니다. Azure Key Vault 암호 이름에 콜론을 허용 하지 않습니다.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

`Load`메서드는 버전 접두사가 있는 공급자를 찾기 위해 자격 증명 모음 암호를 반복 하는 공급자 알고리즘에 의해 호출 됩니다. 에 버전 접두사가 있으면 `Load` 알고리즘은 메서드를 사용 하 여 `GetKey` 비밀 이름의 구성 이름을 반환 합니다. 암호 이름에서 버전 접두사를 제거 하 고 앱의 구성 이름-값 쌍으로 로드 하기 위한 비밀 이름의 나머지를 반환 합니다.

이 방법이 구현 되는 경우:

1. 앱의 프로젝트 파일에 지정 된 앱 버전입니다. 다음 예제에서 응용 프로그램의 버전은로 설정 됩니다 `5.0.0.0` .

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. `<UserSecretsId>`응용 프로그램의 프로젝트 파일에 속성이 있는지 확인 합니다 `{GUID}` . 여기서은 사용자가 제공한 GUID입니다.

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 다음 암호를 로컬로 저장 합니다.

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. 비밀은 다음 Azure CLI 명령을 사용 하 여 Azure Key Vault에 저장 됩니다.

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. 앱이 실행 되 면 키 자격 증명 모음 비밀이 로드 됩니다. 에 대 한 문자열 비밀이 `5000-AppSecret` 응용 프로그램의 프로젝트 파일 ()에 지정 된 앱 버전과 일치 합니다 `5.0.0.0` .

1. `5000`(대시 포함) 버전이 키 이름에서 제거 됩니다. 앱 전체에서 키를 사용 하 여 구성을 읽으면 `AppSecret` 비밀 값이 로드 됩니다.

1. 응용 프로그램의 버전이 프로젝트 파일에서로 변경 되 `5.1.0.0` 고 앱이 다시 실행 되는 경우 반환 되는 암호 값은 `5.1.0.0_secret_value_dev` 개발 환경 및 프로덕션 환경에 `5.1.0.0_secret_value_prod` 있습니다.

> [!NOTE]
> 사용자 고유의 구현을 제공할 수도 있습니다 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . 사용자 지정 클라이언트는 앱에서 클라이언트의 단일 인스턴스를 공유 하도록 허용 합니다.

## <a name="bind-an-array-to-a-class"></a>클래스에 배열 바인딩

공급자는 POCO 배열에 바인딩하기 위해 구성 값을 배열로 읽을 수 있습니다.

키에 콜론 () 구분 기호를 포함할 수 있는 구성 소스에서 읽을 때 `:` 숫자 키 세그먼트는 배열 ( `:0:` ,,)을 구성 하는 키를 구분 하는 데 사용 됩니다 `:1:` &hellip; `:{n}:` . 자세한 내용은 [구성: 클래스에 배열 바인딩](xref:fundamentals/configuration/index#bind-an-array-to-a-class)을 참조 하세요.

Azure Key Vault 키는 콜론을 구분 기호로 사용할 수 없습니다. 이 항목에서 설명 하는 방법에서는 이중 대시 ( `--` )를 계층적 값 (섹션) 구분 기호로 사용 합니다. 배열 키는 이중 대시 및 숫자 키 세그먼트 ( `--0--` ,,)를 사용 하는 Azure Key Vault에 저장 됩니다 `--1--` &hellip; `--{n}--` .

JSON 파일에서 제공 하는 다음과 같은 [Serilog](https://serilog.net/) 로깅 공급자 구성을 검토 합니다. `WriteTo`출력 로깅의 대상을 설명 하는 두 개의 Serilog *싱크* 를 반영 하는 두 개의 개체 리터럴이 배열에 정의 되어 있습니다.

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

위의 JSON 파일에 표시 된 구성은 이중 대시 ( `--` ) 표기법 및 숫자 세그먼트를 사용 하 여 Azure Key Vault에 저장 됩니다.

| 키 | 값 |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>비밀 다시 로드

암호는가 호출 될 때까지 캐시 됩니다 `IConfigurationRoot.Reload()` . 키 자격 증명 모음에서 만료, 사용 안 함 및 업데이트 된 비밀은가 실행 될 때까지 앱에서 적용 되지 않습니다 `Reload` .

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>비활성화 및 만료 된 비밀

비활성화 및 만료 된 비밀은을 throw <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 합니다. 앱이 throw 되지 않도록 하려면 다른 구성 공급자를 사용 하 여 구성을 제공 하거나 사용 하지 않거나 만료 된 암호를 업데이트 합니다.

## <a name="troubleshoot"></a>문제 해결

앱이 공급자를 사용 하 여 구성을 로드 하지 못하면 [ASP.NET Core 로깅 인프라](xref:fundamentals/logging/index)에 오류 메시지가 기록 됩니다. 다음 조건에서는 구성을 로드할 수 없습니다.

* 앱 또는 인증서가 Azure Active Directory에서 올바르게 구성 되지 않았습니다.
* 키 자격 증명 모음이 Azure Key Vault에 없습니다.
* 앱에 키 자격 증명 모음에 액세스할 수 있는 권한이 없습니다.
* 액세스 정책에는 `Get` 및 사용 권한이 포함 되어 있지 않습니다 `List` .
* 키 자격 증명 모음에서 구성 데이터 (이름-값 쌍)의 이름을 잘못 지정 했거나, 누락 되었거나, 사용 하지 않거나, 만료 되었습니다.
* 앱에 잘못 된 키 자격 증명 모음 이름 ( `KeyVaultName` ), AZURE Ad 응용 프로그램 Id () `AzureADApplicationId` 또는 azure ad 인증서 지문 ( `AzureADCertThumbprint` )이 있습니다.
* 로드 하려는 값에 대 한 앱의 구성 키 (이름)가 잘못 되었습니다.
* 키 자격 증명 모음에 앱에 대 한 액세스 정책을 추가 하는 경우 정책이 만들어졌지만 **액세스 정책** UI에서 **저장** 단추가 선택 되지 않았습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault 설명서](/azure/key-vault/)
* [Azure Key Vault 용 HSM 보호 키를 생성 하 고 전송 하는 방법](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient 클래스](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색](/azure/key-vault/quick-create-net)
* [자습서: .NET에서 Azure Windows Virtual Machine에 Azure Key Vault를 사용하는 방법](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
