---
title: ASP.NET Core에서 개발 중인 앱 비밀 안전 저장소
author: rick-anderson
description: ASP.NET Core 앱을 개발 하는 동안 중요 한 정보를 앱 비밀으로 저장 하 고 검색 하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
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
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056870"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="b8fef-103">ASP.NET Core에서 개발 중인 앱 비밀 안전 저장소</span><span class="sxs-lookup"><span data-stu-id="b8fef-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b8fef-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)및 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="b8fef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="b8fef-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8fef-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b8fef-106">이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발 하는 동안 중요 한 데이터를 저장 하 고 검색 하는 기술을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="b8fef-107">암호 또는 기타 중요 한 데이터를 소스 코드에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b8fef-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="b8fef-108">프로덕션 암호는 개발 또는 테스트에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="b8fef-109">암호는 앱과 함께 배포 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="b8fef-110">대신 환경 변수, Azure Key Vault 등의 제어 된 방법을 통해 프로덕션 환경에서 암호를 사용할 수 있습니다. [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)를 사용 하 여 Azure 테스트 및 프로덕션 암호를 저장 하 고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="b8fef-111">환경 변수</span><span class="sxs-lookup"><span data-stu-id="b8fef-111">Environment variables</span></span>

<span data-ttu-id="b8fef-112">환경 변수는 코드 또는 로컬 구성 파일에서 앱 암호의 저장을 방지 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="b8fef-113">환경 변수는 이전에 지정 된 모든 구성 소스에 대 한 구성 값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="b8fef-114">**개별 사용자 계정** 보안이 사용 되는 ASP.NET Core 웹 앱을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="b8fef-115">기본 데이터베이스 연결 문자열은 키가 있는 프로젝트의 파일에 포함 됩니다 *appsettings.json* `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="b8fef-116">기본 연결 문자열은 사용자 모드에서 실행 되며 암호를 요구 하지 않는 LocalDB 용입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="b8fef-117">앱을 배포 하는 동안 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="b8fef-118">환경 변수는 중요 한 자격 증명을 사용 하 여 전체 연결 문자열을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="b8fef-119">환경 변수는 일반적으로 암호화 되지 않은 일반 텍스트로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="b8fef-120">컴퓨터 또는 프로세스가 손상 되 면 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="b8fef-121">사용자 비밀 공개를 방지 하는 추가 조치가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="b8fef-122">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="b8fef-122">Secret Manager</span></span>

<span data-ttu-id="b8fef-123">암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="b8fef-124">이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="b8fef-125">앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="b8fef-126">앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="b8fef-127">앱 비밀이 소스 제어에 체크 인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="b8fef-128">비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="b8fef-129">개발 목적 으로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-129">It's for development purposes only.</span></span> <span data-ttu-id="b8fef-130">키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="b8fef-131">비밀 관리자 도구의 작동 원리</span><span class="sxs-lookup"><span data-stu-id="b8fef-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="b8fef-132">비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 추출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="b8fef-133">이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="b8fef-134">값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 있는 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="b8fef-135">Windows</span><span class="sxs-lookup"><span data-stu-id="b8fef-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="b8fef-136">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="b8fef-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="b8fef-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="b8fef-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="b8fef-138">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="b8fef-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="b8fef-139">위의 파일 경로에서를 `<user_secrets_id>` `UserSecretsId` *.csproj* 파일에 지정 된 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="b8fef-140">암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b8fef-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="b8fef-141">이러한 구현 세부 정보는 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-141">These implementation details may change.</span></span> <span data-ttu-id="b8fef-142">예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="b8fef-143">비밀 저장소 사용</span><span class="sxs-lookup"><span data-stu-id="b8fef-143">Enable secret storage</span></span>

<span data-ttu-id="b8fef-144">비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="b8fef-145">암호 관리자 도구는 `init` .NET Core SDK 3.0.100 이상에서 명령을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="b8fef-146">사용자 암호를 사용 하려면 프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="b8fef-147">이전 명령은 `UserSecretsId` `PropertyGroup` *.csproj* 파일의 내에 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="b8fef-148">기본적으로의 내부 텍스트는 `UserSecretsId` GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="b8fef-149">내부 텍스트는 임의로 이지만 프로젝트에 고유 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="b8fef-150">Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="b8fef-151">이 제스처는 `UserSecretsId` GUID로 채워진 요소를 *.csproj* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="b8fef-152">비밀 설정</span><span class="sxs-lookup"><span data-stu-id="b8fef-152">Set a secret</span></span>

<span data-ttu-id="b8fef-153">키와 해당 값으로 구성 된 앱 암호를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="b8fef-154">암호는 프로젝트의 값과 연결 됩니다 `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="b8fef-155">예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="b8fef-156">앞의 예제에서 콜론은 `Movies` 가 속성을 사용 하는 개체 리터럴이어야 함을 나타냅니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="b8fef-157">암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="b8fef-158">옵션을 사용 `--project` 하 여 *.csproj* 파일이 있는 파일 시스템 경로를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="b8fef-159">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="b8fef-160">Visual Studio의 JSON 구조 평면화</span><span class="sxs-lookup"><span data-stu-id="b8fef-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="b8fef-161">Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 파일 *에secrets.js* 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="b8fef-162">*secrets.js* 의 내용을 저장할 키-값 쌍으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="b8fef-163">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="b8fef-164">JSON 구조는 또는를 통해 수정 된 후에 결합 됩니다 `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="b8fef-165">예를 들어를 실행 하면 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 개체 리터럴이 축소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="b8fef-166">수정 된 파일은 다음과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="b8fef-167">여러 비밀 설정</span><span class="sxs-lookup"><span data-stu-id="b8fef-167">Set multiple secrets</span></span>

<span data-ttu-id="b8fef-168">암호 일괄 처리는 명령에 대해 파이프로 파이프 하 여 설정할 수 있습니다 `set` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="b8fef-169">다음 예제에서는 파일의 내용 *에 대 한input.js* 를 명령으로 파이프 `set` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="b8fef-170">Windows</span><span class="sxs-lookup"><span data-stu-id="b8fef-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="b8fef-171">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="b8fef-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="b8fef-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="b8fef-173">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="b8fef-174">비밀 액세스</span><span class="sxs-lookup"><span data-stu-id="b8fef-174">Access a secret</span></span>

<span data-ttu-id="b8fef-175">[ASP.NET Core 구성 API](xref:fundamentals/configuration/index) 는 암호 관리자 암호에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="b8fef-176">프로젝트에서를 호출 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 하 여 미리 구성 된 기본값을 사용 하 여 호스트의 새 인스턴스를 초기화 하는 경우 사용자 암호 구성 소스가 개발 모드에서 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="b8fef-177">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>가 인 경우를 호출 합니다 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> .</span><span class="sxs-lookup"><span data-stu-id="b8fef-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="b8fef-178">`CreateDefaultBuilder`가 호출 되지 않은 경우를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="b8fef-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="b8fef-179">`AddUserSecrets`다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="b8fef-180">API를 통해 사용자 암호를 검색할 수 있습니다 `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="b8fef-181">POCO에 비밀 매핑</span><span class="sxs-lookup"><span data-stu-id="b8fef-181">Map secrets to a POCO</span></span>

<span data-ttu-id="b8fef-182">전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-183">위의 비밀을 POCO에 매핑하려면 `Configuration` API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="b8fef-184">다음 코드는 사용자 지정 POCO에 바인딩하고 `MovieSettings` 속성 값에 액세스 합니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="b8fef-185">`Movies:ConnectionString`및 `Movies:ServiceApiKey` 암호는의 해당 속성에 매핑됩니다 `MovieSettings` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="b8fef-186">암호를 사용 하 여 문자열 대체</span><span class="sxs-lookup"><span data-stu-id="b8fef-186">String replacement with secrets</span></span>

<span data-ttu-id="b8fef-187">암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="b8fef-188">예를 들어에 저장 된 데이터베이스 연결 문자열은 *appsettings.json* 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="b8fef-189">보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="b8fef-190">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="b8fef-191">`Password`에서 연결 문자열의 키-값 쌍을 제거 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b8fef-191">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="b8fef-192">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="b8fef-193">개체의 속성에 대해 암호의 값을 설정 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 하 여 연결 문자열을 완성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="b8fef-194">암호 나열</span><span class="sxs-lookup"><span data-stu-id="b8fef-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-195">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="b8fef-196">다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="b8fef-197">앞의 예제에서 키 이름의 콜론은 *secrets.js에서* 개체 계층 구조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="b8fef-198">단일 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="b8fef-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-199">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="b8fef-200">키에 연결 된 키-값 쌍을 제거 하기 위해 파일 *에 대 한 앱secrets.js* 수정 되었습니다 `MoviesConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="b8fef-201">`dotnet user-secrets list` 다음 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="b8fef-202">모든 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="b8fef-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-203">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="b8fef-204">앱에 대 한 모든 사용자 비밀이 파일 *의secrets.js* 에서 삭제 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="b8fef-205">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="b8fef-206">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b8fef-206">Additional resources</span></span>

* <span data-ttu-id="b8fef-207">IIS에서 암호 관리자에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b8fef-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b8fef-208">[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)및 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="b8fef-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="b8fef-209">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8fef-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b8fef-210">이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발 하는 동안 중요 한 데이터를 저장 하 고 검색 하는 기술을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="b8fef-211">암호 또는 기타 중요 한 데이터를 소스 코드에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b8fef-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="b8fef-212">프로덕션 암호는 개발 또는 테스트에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="b8fef-213">암호는 앱과 함께 배포 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="b8fef-214">대신 환경 변수, Azure Key Vault 등의 제어 된 방법을 통해 프로덕션 환경에서 암호를 사용할 수 있습니다. [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)를 사용 하 여 Azure 테스트 및 프로덕션 암호를 저장 하 고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="b8fef-215">환경 변수</span><span class="sxs-lookup"><span data-stu-id="b8fef-215">Environment variables</span></span>

<span data-ttu-id="b8fef-216">환경 변수는 코드 또는 로컬 구성 파일에서 앱 암호의 저장을 방지 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="b8fef-217">환경 변수는 이전에 지정 된 모든 구성 소스에 대 한 구성 값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="b8fef-218">**개별 사용자 계정** 보안이 사용 되는 ASP.NET Core 웹 앱을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="b8fef-219">기본 데이터베이스 연결 문자열은 키가 있는 프로젝트의 파일에 포함 됩니다 *appsettings.json* `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="b8fef-220">기본 연결 문자열은 사용자 모드에서 실행 되며 암호를 요구 하지 않는 LocalDB 용입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="b8fef-221">앱을 배포 하는 동안 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="b8fef-222">환경 변수는 중요 한 자격 증명을 사용 하 여 전체 연결 문자열을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="b8fef-223">환경 변수는 일반적으로 암호화 되지 않은 일반 텍스트로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="b8fef-224">컴퓨터 또는 프로세스가 손상 되 면 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="b8fef-225">사용자 비밀 공개를 방지 하는 추가 조치가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="b8fef-226">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="b8fef-226">Secret Manager</span></span>

<span data-ttu-id="b8fef-227">암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="b8fef-228">이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="b8fef-229">앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="b8fef-230">앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="b8fef-231">앱 비밀이 소스 제어에 체크 인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="b8fef-232">비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="b8fef-233">개발 목적 으로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-233">It's for development purposes only.</span></span> <span data-ttu-id="b8fef-234">키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="b8fef-235">비밀 관리자 도구의 작동 원리</span><span class="sxs-lookup"><span data-stu-id="b8fef-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="b8fef-236">비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 추출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="b8fef-237">이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="b8fef-238">값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 있는 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="b8fef-239">Windows</span><span class="sxs-lookup"><span data-stu-id="b8fef-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="b8fef-240">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="b8fef-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="b8fef-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="b8fef-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="b8fef-242">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="b8fef-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="b8fef-243">위의 파일 경로에서를 `<user_secrets_id>` `UserSecretsId` *.csproj* 파일에 지정 된 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="b8fef-244">암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="b8fef-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="b8fef-245">이러한 구현 세부 정보는 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-245">These implementation details may change.</span></span> <span data-ttu-id="b8fef-246">예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="b8fef-247">비밀 저장소 사용</span><span class="sxs-lookup"><span data-stu-id="b8fef-247">Enable secret storage</span></span>

<span data-ttu-id="b8fef-248">비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="b8fef-249">사용자 암호를 사용 하려면 `UserSecretsId` .csproj 파일의 내에서 요소를 정의 `PropertyGroup` 합니다. *.csproj*</span><span class="sxs-lookup"><span data-stu-id="b8fef-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="b8fef-250">의 내부 텍스트는 `UserSecretsId` 임의로 이지만 프로젝트에 고유 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="b8fef-251">개발자는 일반적으로에 대 한 GUID를 생성 `UserSecretsId` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="b8fef-252">Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="b8fef-253">이 제스처는 `UserSecretsId` GUID로 채워진 요소를 *.csproj* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="b8fef-254">비밀 설정</span><span class="sxs-lookup"><span data-stu-id="b8fef-254">Set a secret</span></span>

<span data-ttu-id="b8fef-255">키와 해당 값으로 구성 된 앱 암호를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="b8fef-256">암호는 프로젝트의 값과 연결 됩니다 `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="b8fef-257">예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="b8fef-258">앞의 예제에서 콜론은 `Movies` 가 속성을 사용 하는 개체 리터럴이어야 함을 나타냅니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="b8fef-259">암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="b8fef-260">옵션을 사용 `--project` 하 여 *.csproj* 파일이 있는 파일 시스템 경로를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="b8fef-261">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="b8fef-262">Visual Studio의 JSON 구조 평면화</span><span class="sxs-lookup"><span data-stu-id="b8fef-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="b8fef-263">Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 파일 *에secrets.js* 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="b8fef-264">*secrets.js* 의 내용을 저장할 키-값 쌍으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="b8fef-265">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="b8fef-266">JSON 구조는 또는를 통해 수정 된 후에 결합 됩니다 `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="b8fef-267">예를 들어를 실행 하면 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 개체 리터럴이 축소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="b8fef-268">수정 된 파일은 다음과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="b8fef-269">여러 비밀 설정</span><span class="sxs-lookup"><span data-stu-id="b8fef-269">Set multiple secrets</span></span>

<span data-ttu-id="b8fef-270">암호 일괄 처리는 명령에 대해 파이프로 파이프 하 여 설정할 수 있습니다 `set` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="b8fef-271">다음 예제에서는 파일의 내용 *에 대 한input.js* 를 명령으로 파이프 `set` 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="b8fef-272">Windows</span><span class="sxs-lookup"><span data-stu-id="b8fef-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="b8fef-273">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="b8fef-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="b8fef-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="b8fef-275">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="b8fef-276">비밀 액세스</span><span class="sxs-lookup"><span data-stu-id="b8fef-276">Access a secret</span></span>

<span data-ttu-id="b8fef-277">[ASP.NET Core 구성 API](xref:fundamentals/configuration/index) 는 암호 관리자 암호에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="b8fef-278">프로젝트가 .NET Framework 대상으로 하는 경우 [Microsoft.Extensions.Configuration를 설치 합니다. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="b8fef-279">ASP.NET Core 2.0 이상에서는 프로젝트에서를 호출 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 하 여 미리 구성 된 기본값을 가진 호스트의 새 인스턴스를 초기화 하는 경우 사용자 암호 구성 소스가 개발 모드에서 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="b8fef-280">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>가 인 경우를 호출 합니다 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> .</span><span class="sxs-lookup"><span data-stu-id="b8fef-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="b8fef-281">`CreateDefaultBuilder`가 호출 되지 않은 경우 생성자에서를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="b8fef-282">`AddUserSecrets`다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="b8fef-283">API를 통해 사용자 암호를 검색할 수 있습니다 `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="b8fef-284">POCO에 비밀 매핑</span><span class="sxs-lookup"><span data-stu-id="b8fef-284">Map secrets to a POCO</span></span>

<span data-ttu-id="b8fef-285">전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-286">위의 비밀을 POCO에 매핑하려면 `Configuration` API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="b8fef-287">다음 코드는 사용자 지정 POCO에 바인딩하고 `MovieSettings` 속성 값에 액세스 합니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="b8fef-288">`Movies:ConnectionString`및 `Movies:ServiceApiKey` 암호는의 해당 속성에 매핑됩니다 `MovieSettings` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="b8fef-289">암호를 사용 하 여 문자열 대체</span><span class="sxs-lookup"><span data-stu-id="b8fef-289">String replacement with secrets</span></span>

<span data-ttu-id="b8fef-290">암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="b8fef-291">예를 들어에 저장 된 데이터베이스 연결 문자열은 *appsettings.json* 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="b8fef-292">보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="b8fef-293">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="b8fef-294">`Password`에서 연결 문자열의 키-값 쌍을 제거 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b8fef-294">Remove the `Password` key-value pair from the connection string in *appsettings.json* .</span></span> <span data-ttu-id="b8fef-295">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="b8fef-296">개체의 속성에 대해 암호의 값을 설정 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 하 여 연결 문자열을 완성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="b8fef-297">암호 나열</span><span class="sxs-lookup"><span data-stu-id="b8fef-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-298">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="b8fef-299">다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="b8fef-300">앞의 예제에서 키 이름의 콜론은 *secrets.js에서* 개체 계층 구조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="b8fef-301">단일 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="b8fef-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-302">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="b8fef-303">키에 연결 된 키-값 쌍을 제거 하기 위해 파일 *에 대 한 앱secrets.js* 수정 되었습니다 `MoviesConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="b8fef-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="b8fef-304">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="b8fef-305">모든 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="b8fef-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="b8fef-306">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="b8fef-307">앱에 대 한 모든 사용자 비밀이 파일 *의secrets.js* 에서 삭제 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="b8fef-308">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b8fef-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="b8fef-309">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b8fef-309">Additional resources</span></span>

* <span data-ttu-id="b8fef-310">IIS에서 암호 관리자에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b8fef-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end