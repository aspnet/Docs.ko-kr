---
title: ASP.NET Core에서 개발 중인 앱 비밀 안전 저장소
author: rick-anderson
description: ASP.NET Core 앱을 개발 하는 동안 중요 한 정보를 저장 하 고 검색 하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
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
uid: security/app-secrets
ms.openlocfilehash: 63032895ce45ad096612a8c39a2709628c12790f
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486202"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core에서 개발 중인 앱 비밀 안전 저장소

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)및 [Scott addie](https://github.com/scottaddie)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱에 대 한 중요 한 데이터를 관리 하는 방법을 설명 합니다. 암호 또는 기타 중요 한 데이터를 소스 코드에 저장 하지 마세요. 프로덕션 암호는 개발 또는 테스트에 사용할 수 없습니다. 암호는 앱과 함께 배포 해서는 안 됩니다. 대신 환경 변수 또는 Azure Key Vault 같은 제어 된 방법을 통해 프로덕션 비밀에 액세스 해야 합니다. [Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

환경 변수는 코드 또는 로컬 구성 파일에서 앱 암호의 저장을 방지 하는 데 사용 됩니다. 환경 변수는 이전에 지정 된 모든 구성 소스에 대 한 구성 값을 재정의 합니다.

**개별 사용자 계정** 보안이 사용 되는 ASP.NET Core 웹 앱을 고려 합니다. 기본 데이터베이스 연결 문자열은 키가 있는 프로젝트의 파일에 포함 됩니다 *appsettings.json* `DefaultConnection` . 기본 연결 문자열은 사용자 모드에서 실행 되며 암호를 요구 하지 않는 LocalDB 용입니다. 앱을 배포 하는 동안 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다. 환경 변수는 중요 한 자격 증명을 사용 하 여 전체 연결 문자열을 저장할 수 있습니다.

> [!WARNING]
> 환경 변수는 일반적으로 암호화 되지 않은 일반 텍스트로 저장 됩니다. 컴퓨터 또는 프로세스가 손상 되 면 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다. 사용자 비밀 공개를 방지 하는 추가 조치가 필요할 수 있습니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>비밀 관리자

암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다. 이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다. 앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다. 앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다. 앱 비밀이 소스 제어에 체크 인 되지 않습니다.

> [!WARNING]
> 비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다. 개발 목적 으로만 사용 됩니다. 키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.

## <a name="how-the-secret-manager-tool-works"></a>비밀 관리자 도구의 작동 원리

비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 숨깁니다. 이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다. 값은 로컬 컴퓨터의 사용자 프로필 폴더에 있는 JSON 파일에 저장 됩니다.

# <a name="windows"></a>[Windows](#tab/windows)

파일 시스템 경로:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

파일 시스템 경로:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

위의 파일 경로에서를 `<user_secrets_id>` `UserSecretsId` 프로젝트 파일에 지정 된 값으로 바꿉니다.

암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요. 이러한 구현 세부 정보는 변경 될 수 있습니다. 예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.

## <a name="enable-secret-storage"></a>비밀 저장소 사용

비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.

암호 관리자 도구는 `init` .NET Core SDK 3.0.100 이상에서 명령을 포함 합니다. 사용자 암호를 사용 하려면 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets init
```

이전 명령은 `UserSecretsId` 프로젝트 파일의 내에 요소를 추가 합니다 `PropertyGroup` . 기본적으로의 내부 텍스트는 `UserSecretsId` GUID입니다. 내부 텍스트는 임의로 이지만 프로젝트에 고유 합니다.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다. 이 제스처는 `UserSecretsId` GUID로 채워진 요소를 프로젝트 파일에 추가 합니다.

## <a name="set-a-secret"></a>비밀 설정

키와 해당 값으로 구성 된 앱 암호를 정의 합니다. 암호는 프로젝트의 값과 연결 됩니다 `UserSecretsId` . 예를 들어 프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

앞의 예제에서 콜론은 `Movies` 가 속성을 사용 하는 개체 리터럴이어야 함을 나타냅니다 `ServiceApiKey` .

암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다. 옵션을 사용 `--project` 하 여 프로젝트 파일이 있는 파일 시스템 경로를 제공 합니다. 예를 들어:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio의 JSON 구조 평면화

Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 파일 *에secrets.js* 를 엽니다. *secrets.js* 의 내용을 저장할 키-값 쌍으로 바꿉니다. 예를 들어:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 구조는 또는를 통해 수정 된 후에 결합 됩니다 `dotnet user-secrets remove` `dotnet user-secrets set` . 예를 들어를 실행 하면 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 개체 리터럴이 축소 됩니다. 수정 된 파일은 다음 JSON과 유사 합니다.

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>여러 비밀 설정

암호 일괄 처리는 명령에 대해 파이프로 파이프 하 여 설정할 수 있습니다 `set` . 다음 예제에서는 파일의 내용 *에 대 한input.js* 를 명령으로 파이프 `set` 합니다.

# <a name="windows"></a>[Windows](#tab/windows)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>비밀 액세스

비밀에 액세스 하려면 다음 단계를 완료 합니다.

1. [사용자 암호 구성 원본 등록](#register-the-user-secrets-configuration-source)
1. [구성 API를 통해 암호를 읽습니다.](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a>사용자 암호 구성 원본 등록

사용자 암호 [구성 공급자](/dotnet/core/extensions/configuration-providers) 는 .NET [구성 API](xref:fundamentals/configuration/index)를 사용 하 여 적절 한 구성 소스를 등록 합니다.

사용자 암호 구성 소스는 프로젝트가를 호출할 때 개발 모드에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> . `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>가 인 경우를 호출 합니다 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> .

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

`CreateDefaultBuilder`가 호출 되지 않은 경우에서를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> . `AddUserSecrets`다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만를 호출 합니다.

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a>구성 API를 통해 암호를 읽습니다.

사용자 암호 구성 원본이 등록 되어 있으면 .NET 구성 API에서 비밀을 읽을 수 있습니다. [생성자 주입](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) 은 .NET 구성 API에 대 한 액세스 권한을 얻는 데 사용할 수 있습니다. 키를 읽는 다음 예제를 고려 합니다 `Movies:ServiceApiKey` .

**시작 클래스:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

**Razor 페이지 페이지 모델:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

자세한 내용은 [ Razor 페이지의 시작 및 액세스 구성](xref:fundamentals/configuration/index#access-configuration-in-razor-pages) [에서 액세스 구성](xref:fundamentals/configuration/index#access-configuration-in-startup) 을 참조 하세요.

## <a name="map-secrets-to-a-poco"></a>POCO에 비밀 매핑

전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

위의 비밀을 POCO에 매핑하려면 .NET 구성 API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다. 다음 코드는 사용자 지정 POCO에 바인딩하고 `MovieSettings` 속성 값에 액세스 합니다 `ServiceApiKey` .

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`및 `Movies:ServiceApiKey` 암호는의 해당 속성에 매핑됩니다 `MovieSettings` .

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>암호를 사용 하 여 문자열 대체

암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다. 예를 들어에 저장 된 데이터베이스 연결 문자열은 *appsettings.json* 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다. 예를 들어:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`에서 연결 문자열의 키-값 쌍을 제거 합니다 *appsettings.json* . 예를 들어:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

개체의 속성에 대해 암호의 값을 설정 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 하 여 연결 문자열을 완성할 수 있습니다.

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>암호 나열

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets list
```

다음과 같은 출력이 표시됩니다.

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

앞의 예제에서 키 이름의 콜론은 *secrets.js에서* 개체 계층 구조를 나타냅니다.

## <a name="remove-a-single-secret"></a>단일 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

키에 연결 된 키-값 쌍을 제거 하기 위해 파일 *에 대 한 앱secrets.js* 수정 되었습니다 `MoviesConnectionString` .

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` 다음 메시지를 표시 합니다.

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>모든 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets clear
```

앱에 대 한 모든 사용자 비밀이 파일 *의secrets.js* 에서 삭제 되었습니다.

```json
{}
```

`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>추가 리소스

* IIS에서 사용자 암호에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)및 [Scott addie](https://github.com/scottaddie)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱에 대 한 중요 한 데이터를 관리 하는 방법을 설명 합니다. 암호 또는 기타 중요 한 데이터를 소스 코드에 저장 하지 마세요. 프로덕션 암호는 개발 또는 테스트에 사용할 수 없습니다. 암호는 앱과 함께 배포 해서는 안 됩니다. 대신 환경 변수 또는 Azure Key Vault 같은 제어 된 방법을 통해 프로덕션 비밀에 액세스 해야 합니다. [Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

환경 변수는 코드 또는 로컬 구성 파일에서 앱 암호의 저장을 방지 하는 데 사용 됩니다. 환경 변수는 이전에 지정 된 모든 구성 소스에 대 한 구성 값을 재정의 합니다.

**개별 사용자 계정** 보안이 사용 되는 ASP.NET Core 웹 앱을 고려 합니다. 기본 데이터베이스 연결 문자열은 키가 있는 프로젝트의 파일에 포함 됩니다 *appsettings.json* `DefaultConnection` . 기본 연결 문자열은 사용자 모드에서 실행 되며 암호를 요구 하지 않는 LocalDB 용입니다. 앱을 배포 하는 동안 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다. 환경 변수는 중요 한 자격 증명을 사용 하 여 전체 연결 문자열을 저장할 수 있습니다.

> [!WARNING]
> 환경 변수는 일반적으로 암호화 되지 않은 일반 텍스트로 저장 됩니다. 컴퓨터 또는 프로세스가 손상 되 면 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다. 사용자 비밀 공개를 방지 하는 추가 조치가 필요할 수 있습니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>비밀 관리자

암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다. 이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다. 앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다. 앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다. 앱 비밀이 소스 제어에 체크 인 되지 않습니다.

> [!WARNING]
> 비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다. 개발 목적 으로만 사용 됩니다. 키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.

## <a name="how-the-secret-manager-tool-works"></a>비밀 관리자 도구의 작동 원리

비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 숨깁니다. 이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다. 값은 로컬 컴퓨터의 사용자 프로필 폴더에 있는 JSON 파일에 저장 됩니다.

# <a name="windows"></a>[Windows](#tab/windows)

파일 시스템 경로:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

파일 시스템 경로:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

위의 파일 경로에서를 `<user_secrets_id>` `UserSecretsId` 프로젝트 파일에 지정 된 값으로 바꿉니다.

암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요. 이러한 구현 세부 정보는 변경 될 수 있습니다. 예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.

## <a name="enable-secret-storage"></a>비밀 저장소 사용

비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.

사용자 암호를 사용 하려면 `UserSecretsId` 프로젝트 파일의 내에서 요소를 정의 `PropertyGroup` 합니다. 의 내부 텍스트는 `UserSecretsId` 임의로 이지만 프로젝트에 고유 합니다. 개발자는 일반적으로에 대 한 GUID를 생성 `UserSecretsId` 합니다.

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다. 이 제스처는 `UserSecretsId` GUID로 채워진 요소를 프로젝트 파일에 추가 합니다.

## <a name="set-a-secret"></a>비밀 설정

키와 해당 값으로 구성 된 앱 암호를 정의 합니다. 암호는 프로젝트의 값과 연결 됩니다 `UserSecretsId` . 예를 들어 프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

앞의 예제에서 콜론은 `Movies` 가 속성을 사용 하는 개체 리터럴이어야 함을 나타냅니다 `ServiceApiKey` .

암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다. 옵션을 사용 `--project` 하 여 프로젝트 파일이 있는 파일 시스템 경로를 제공 합니다. 예를 들어:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio의 JSON 구조 평면화

Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 파일 *에secrets.js* 를 엽니다. *secrets.js* 의 내용을 저장할 키-값 쌍으로 바꿉니다. 예를 들어:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 구조는 또는를 통해 수정 된 후에 결합 됩니다 `dotnet user-secrets remove` `dotnet user-secrets set` . 예를 들어를 실행 하면 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 개체 리터럴이 축소 됩니다. 수정 된 파일은 다음 JSON과 유사 합니다.

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>여러 비밀 설정

암호 일괄 처리는 명령에 대해 파이프로 파이프 하 여 설정할 수 있습니다 `set` . 다음 예제에서는 파일의 내용 *에 대 한input.js* 를 명령으로 파이프 `set` 합니다.

# <a name="windows"></a>[Windows](#tab/windows)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>비밀 액세스

[구성 API](xref:fundamentals/configuration/index) 는 사용자 암호에 대 한 액세스를 제공 합니다.

프로젝트가 .NET Framework 대상으로 하는 경우 [Microsoft.Extensions.Configuration를 설치 합니다. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 패키지입니다.

ASP.NET Core 2.0 이상에서는 프로젝트가를 호출할 때 사용자 암호 구성 소스가 개발 모드에서 자동으로 추가 됩니다 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> . `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>가 인 경우를 호출 합니다 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> .

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

`CreateDefaultBuilder`가 호출 되지 않은 경우 생성자에서를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` . `AddUserSecrets`다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만를 호출 합니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

사용자 암호는 .NET 구성 API를 통해 검색할 수 있습니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>POCO에 비밀 매핑

전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

위의 비밀을 POCO에 매핑하려면 .NET 구성 API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다. 다음 코드는 사용자 지정 POCO에 바인딩하고 `MovieSettings` 속성 값에 액세스 합니다 `ServiceApiKey` .

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`및 `Movies:ServiceApiKey` 암호는의 해당 속성에 매핑됩니다 `MovieSettings` .

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>암호를 사용 하 여 문자열 대체

암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다. 예를 들어에 저장 된 데이터베이스 연결 문자열은 *appsettings.json* 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다. 예를 들어:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`에서 연결 문자열의 키-값 쌍을 제거 합니다 *appsettings.json* . 예를 들어:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

개체의 속성에 대해 암호의 값을 설정 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 하 여 연결 문자열을 완성할 수 있습니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>암호 나열

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets list
```

다음과 같은 출력이 표시됩니다.

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

앞의 예제에서 키 이름의 콜론은 *secrets.js에서* 개체 계층 구조를 나타냅니다.

## <a name="remove-a-single-secret"></a>단일 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

키에 연결 된 키-값 쌍을 제거 하기 위해 파일 *에 대 한 앱secrets.js* 수정 되었습니다 `MoviesConnectionString` .

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>모든 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets clear
```

앱에 대 한 모든 사용자 비밀이 파일 *의secrets.js* 에서 삭제 되었습니다.

```json
{}
```

`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>추가 리소스

* IIS에서 사용자 암호에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end