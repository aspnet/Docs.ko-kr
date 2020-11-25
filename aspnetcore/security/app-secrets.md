---
title: ASP.NET Core에서 개발 중인 앱 비밀 안전 저장소
author: rick-anderson
description: ASP.NET Core 앱을 개발 하는 동안 중요 한 정보를 저장 하 고 검색 하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc, contperfq2
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
ms.openlocfilehash: 99b7b04076206f95c04da79283010beafdd1cc88
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035855"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="f7a56-103">ASP.NET Core에서 개발 중인 앱 비밀 안전 저장소</span><span class="sxs-lookup"><span data-stu-id="f7a56-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7a56-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)및 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f7a56-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="f7a56-105">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7a56-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f7a56-106">이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱에 대 한 중요 한 데이터를 관리 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="f7a56-107">암호 또는 기타 중요 한 데이터를 소스 코드에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f7a56-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="f7a56-108">프로덕션 암호는 개발 또는 테스트에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="f7a56-109">암호는 앱과 함께 배포 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="f7a56-110">대신 환경 변수 또는 Azure Key Vault 같은 제어 된 방법을 통해 프로덕션 비밀에 액세스 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="f7a56-111">[Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="f7a56-112">환경 변수</span><span class="sxs-lookup"><span data-stu-id="f7a56-112">Environment variables</span></span>

<span data-ttu-id="f7a56-113">환경 변수는 코드 또는 로컬 구성 파일에서 앱 암호의 저장을 방지 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="f7a56-114">환경 변수는 이전에 지정 된 모든 구성 소스에 대 한 구성 값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="f7a56-115">**개별 사용자 계정** 보안이 사용 되는 ASP.NET Core 웹 앱을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="f7a56-116">기본 데이터베이스 연결 문자열은 키가 있는 프로젝트의 파일에 포함 됩니다 *appsettings.json* `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="f7a56-117">기본 연결 문자열은 사용자 모드에서 실행 되며 암호를 요구 하지 않는 LocalDB 용입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="f7a56-118">앱을 배포 하는 동안 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="f7a56-119">환경 변수는 중요 한 자격 증명을 사용 하 여 전체 연결 문자열을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="f7a56-120">환경 변수는 일반적으로 암호화 되지 않은 일반 텍스트로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="f7a56-121">컴퓨터 또는 프로세스가 손상 되 면 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="f7a56-122">사용자 비밀 공개를 방지 하는 추가 조치가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="f7a56-123">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="f7a56-123">Secret Manager</span></span>

<span data-ttu-id="f7a56-124">암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="f7a56-125">이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="f7a56-126">앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="f7a56-127">앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="f7a56-128">앱 비밀이 소스 제어에 체크 인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="f7a56-129">비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="f7a56-130">개발 목적 으로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-130">It's for development purposes only.</span></span> <span data-ttu-id="f7a56-131">키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="f7a56-132">비밀 관리자 도구의 작동 원리</span><span class="sxs-lookup"><span data-stu-id="f7a56-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="f7a56-133">비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="f7a56-134">이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="f7a56-135">값은 로컬 컴퓨터의 사용자 프로필 폴더에 있는 JSON 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="f7a56-136">Windows</span><span class="sxs-lookup"><span data-stu-id="f7a56-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="f7a56-137">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="f7a56-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="f7a56-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="f7a56-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="f7a56-139">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="f7a56-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="f7a56-140">위의 파일 경로에서를 `<user_secrets_id>` `UserSecretsId` 프로젝트 파일에 지정 된 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="f7a56-141">암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f7a56-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="f7a56-142">이러한 구현 세부 정보는 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-142">These implementation details may change.</span></span> <span data-ttu-id="f7a56-143">예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="f7a56-144">비밀 저장소 사용</span><span class="sxs-lookup"><span data-stu-id="f7a56-144">Enable secret storage</span></span>

<span data-ttu-id="f7a56-145">비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="f7a56-146">암호 관리자 도구는 `init` .NET Core SDK 3.0.100 이상에서 명령을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="f7a56-147">사용자 암호를 사용 하려면 프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="f7a56-148">이전 명령은 `UserSecretsId` 프로젝트 파일의 내에 요소를 추가 합니다 `PropertyGroup` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="f7a56-149">기본적으로의 내부 텍스트는 `UserSecretsId` GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="f7a56-150">내부 텍스트는 임의로 이지만 프로젝트에 고유 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="f7a56-151">Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="f7a56-152">이 제스처는 `UserSecretsId` GUID로 채워진 요소를 프로젝트 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="f7a56-153">비밀 설정</span><span class="sxs-lookup"><span data-stu-id="f7a56-153">Set a secret</span></span>

<span data-ttu-id="f7a56-154">키와 해당 값으로 구성 된 앱 암호를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="f7a56-155">암호는 프로젝트의 값과 연결 됩니다 `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="f7a56-156">예를 들어 프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="f7a56-157">앞의 예제에서 콜론은 `Movies` 가 속성을 사용 하는 개체 리터럴이어야 함을 나타냅니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="f7a56-158">암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="f7a56-159">옵션을 사용 `--project` 하 여 프로젝트 파일이 있는 파일 시스템 경로를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="f7a56-160">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="f7a56-161">Visual Studio의 JSON 구조 평면화</span><span class="sxs-lookup"><span data-stu-id="f7a56-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="f7a56-162">Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 파일 *에secrets.js* 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="f7a56-163">*secrets.js* 의 내용을 저장할 키-값 쌍으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="f7a56-164">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="f7a56-165">JSON 구조는 또는를 통해 수정 된 후에 결합 됩니다 `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="f7a56-166">예를 들어를 실행 하면 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 개체 리터럴이 축소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="f7a56-167">수정 된 파일은 다음 JSON과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="f7a56-168">여러 비밀 설정</span><span class="sxs-lookup"><span data-stu-id="f7a56-168">Set multiple secrets</span></span>

<span data-ttu-id="f7a56-169">암호 일괄 처리는 명령에 대해 파이프로 파이프 하 여 설정할 수 있습니다 `set` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="f7a56-170">다음 예제에서는 파일의 내용 *에 대 한input.js* 를 명령으로 파이프 `set` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="f7a56-171">Windows</span><span class="sxs-lookup"><span data-stu-id="f7a56-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="f7a56-172">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="f7a56-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="f7a56-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="f7a56-174">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="f7a56-175">비밀 액세스</span><span class="sxs-lookup"><span data-stu-id="f7a56-175">Access a secret</span></span>

<span data-ttu-id="f7a56-176">비밀에 액세스 하려면 다음 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="f7a56-177">사용자 암호 구성 원본 등록</span><span class="sxs-lookup"><span data-stu-id="f7a56-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="f7a56-178">구성 API를 통해 암호를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="f7a56-179">사용자 암호 구성 원본 등록</span><span class="sxs-lookup"><span data-stu-id="f7a56-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="f7a56-180">사용자 암호 [구성 공급자](/dotnet/core/extensions/configuration-providers) 는 .NET [구성 API](xref:fundamentals/configuration/index)를 사용 하 여 적절 한 구성 소스를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="f7a56-181">사용자 암호 구성 소스는 프로젝트가를 호출할 때 개발 모드에서 자동으로 추가 됩니다 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="f7a56-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="f7a56-182">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>가 인 경우를 호출 합니다 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> .</span><span class="sxs-lookup"><span data-stu-id="f7a56-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="f7a56-183">`CreateDefaultBuilder`가 호출 되지 않은 경우에서를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="f7a56-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="f7a56-184">`AddUserSecrets`다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="f7a56-185">구성 API를 통해 암호를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="f7a56-186">사용자 암호 구성 원본이 등록 되어 있으면 .NET 구성 API에서 비밀을 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="f7a56-187">[생성자 주입](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) 은 .NET 구성 API에 대 한 액세스 권한을 얻는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="f7a56-188">키를 읽는 다음 예제를 고려 합니다 `Movies:ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="f7a56-189">**시작 클래스:**</span><span class="sxs-lookup"><span data-stu-id="f7a56-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="f7a56-190">**Razor 페이지 페이지 모델:**</span><span class="sxs-lookup"><span data-stu-id="f7a56-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="f7a56-191">자세한 내용은 [ Razor 페이지의 시작 및 액세스 구성](xref:fundamentals/configuration/index#access-configuration-in-razor-pages) [에서 액세스 구성](xref:fundamentals/configuration/index#access-configuration-in-startup) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f7a56-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="f7a56-192">POCO에 비밀 매핑</span><span class="sxs-lookup"><span data-stu-id="f7a56-192">Map secrets to a POCO</span></span>

<span data-ttu-id="f7a56-193">전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-194">위의 비밀을 POCO에 매핑하려면 .NET 구성 API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="f7a56-195">다음 코드는 사용자 지정 POCO에 바인딩하고 `MovieSettings` 속성 값에 액세스 합니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="f7a56-196">`Movies:ConnectionString`및 `Movies:ServiceApiKey` 암호는의 해당 속성에 매핑됩니다 `MovieSettings` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="f7a56-197">암호를 사용 하 여 문자열 대체</span><span class="sxs-lookup"><span data-stu-id="f7a56-197">String replacement with secrets</span></span>

<span data-ttu-id="f7a56-198">암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="f7a56-199">예를 들어에 저장 된 데이터베이스 연결 문자열은 *appsettings.json* 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="f7a56-200">보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="f7a56-201">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="f7a56-202">`Password`에서 연결 문자열의 키-값 쌍을 제거 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f7a56-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="f7a56-203">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="f7a56-204">개체의 속성에 대해 암호의 값을 설정 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 하 여 연결 문자열을 완성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="f7a56-205">암호 나열</span><span class="sxs-lookup"><span data-stu-id="f7a56-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-206">프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="f7a56-207">다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="f7a56-208">앞의 예제에서 키 이름의 콜론은 *secrets.js에서* 개체 계층 구조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="f7a56-209">단일 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="f7a56-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-210">프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="f7a56-211">키에 연결 된 키-값 쌍을 제거 하기 위해 파일 *에 대 한 앱secrets.js* 수정 되었습니다 `MoviesConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="f7a56-212">`dotnet user-secrets list` 다음 메시지를 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="f7a56-213">모든 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="f7a56-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-214">프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="f7a56-215">앱에 대 한 모든 사용자 비밀이 파일 *의secrets.js* 에서 삭제 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="f7a56-216">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="f7a56-217">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f7a56-217">Additional resources</span></span>

* <span data-ttu-id="f7a56-218">IIS에서 사용자 암호에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f7a56-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7a56-219">[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)및 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f7a56-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="f7a56-220">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7a56-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f7a56-221">이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱에 대 한 중요 한 데이터를 관리 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="f7a56-222">암호 또는 기타 중요 한 데이터를 소스 코드에 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f7a56-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="f7a56-223">프로덕션 암호는 개발 또는 테스트에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="f7a56-224">암호는 앱과 함께 배포 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="f7a56-225">대신 환경 변수 또는 Azure Key Vault 같은 제어 된 방법을 통해 프로덕션 비밀에 액세스 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="f7a56-226">[Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="f7a56-227">환경 변수</span><span class="sxs-lookup"><span data-stu-id="f7a56-227">Environment variables</span></span>

<span data-ttu-id="f7a56-228">환경 변수는 코드 또는 로컬 구성 파일에서 앱 암호의 저장을 방지 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="f7a56-229">환경 변수는 이전에 지정 된 모든 구성 소스에 대 한 구성 값을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="f7a56-230">**개별 사용자 계정** 보안이 사용 되는 ASP.NET Core 웹 앱을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="f7a56-231">기본 데이터베이스 연결 문자열은 키가 있는 프로젝트의 파일에 포함 됩니다 *appsettings.json* `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="f7a56-232">기본 연결 문자열은 사용자 모드에서 실행 되며 암호를 요구 하지 않는 LocalDB 용입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="f7a56-233">앱을 배포 하는 동안 `DefaultConnection` 환경 변수의 값으로 키 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="f7a56-234">환경 변수는 중요 한 자격 증명을 사용 하 여 전체 연결 문자열을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="f7a56-235">환경 변수는 일반적으로 암호화 되지 않은 일반 텍스트로 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="f7a56-236">컴퓨터 또는 프로세스가 손상 되 면 신뢰할 수 없는 당사자가 환경 변수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="f7a56-237">사용자 비밀 공개를 방지 하는 추가 조치가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="f7a56-238">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="f7a56-238">Secret Manager</span></span>

<span data-ttu-id="f7a56-239">암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="f7a56-240">이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="f7a56-241">앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="f7a56-242">앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="f7a56-243">앱 비밀이 소스 제어에 체크 인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="f7a56-244">비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="f7a56-245">개발 목적 으로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-245">It's for development purposes only.</span></span> <span data-ttu-id="f7a56-246">키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="f7a56-247">비밀 관리자 도구의 작동 원리</span><span class="sxs-lookup"><span data-stu-id="f7a56-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="f7a56-248">비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 숨깁니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="f7a56-249">이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="f7a56-250">값은 로컬 컴퓨터의 사용자 프로필 폴더에 있는 JSON 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="f7a56-251">Windows</span><span class="sxs-lookup"><span data-stu-id="f7a56-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="f7a56-252">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="f7a56-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="f7a56-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="f7a56-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="f7a56-254">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="f7a56-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="f7a56-255">위의 파일 경로에서를 `<user_secrets_id>` `UserSecretsId` 프로젝트 파일에 지정 된 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="f7a56-256">암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f7a56-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="f7a56-257">이러한 구현 세부 정보는 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-257">These implementation details may change.</span></span> <span data-ttu-id="f7a56-258">예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="f7a56-259">비밀 저장소 사용</span><span class="sxs-lookup"><span data-stu-id="f7a56-259">Enable secret storage</span></span>

<span data-ttu-id="f7a56-260">비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="f7a56-261">사용자 암호를 사용 하려면 `UserSecretsId` 프로젝트 파일의 내에서 요소를 정의 `PropertyGroup` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="f7a56-262">의 내부 텍스트는 `UserSecretsId` 임의로 이지만 프로젝트에 고유 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="f7a56-263">개발자는 일반적으로에 대 한 GUID를 생성 `UserSecretsId` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="f7a56-264">Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="f7a56-265">이 제스처는 `UserSecretsId` GUID로 채워진 요소를 프로젝트 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="f7a56-266">비밀 설정</span><span class="sxs-lookup"><span data-stu-id="f7a56-266">Set a secret</span></span>

<span data-ttu-id="f7a56-267">키와 해당 값으로 구성 된 앱 암호를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="f7a56-268">암호는 프로젝트의 값과 연결 됩니다 `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="f7a56-269">예를 들어 프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="f7a56-270">앞의 예제에서 콜론은 `Movies` 가 속성을 사용 하는 개체 리터럴이어야 함을 나타냅니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="f7a56-271">암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="f7a56-272">옵션을 사용 `--project` 하 여 프로젝트 파일이 있는 파일 시스템 경로를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="f7a56-273">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="f7a56-274">Visual Studio의 JSON 구조 평면화</span><span class="sxs-lookup"><span data-stu-id="f7a56-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="f7a56-275">Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 파일 *에secrets.js* 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="f7a56-276">*secrets.js* 의 내용을 저장할 키-값 쌍으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="f7a56-277">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="f7a56-278">JSON 구조는 또는를 통해 수정 된 후에 결합 됩니다 `dotnet user-secrets remove` `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="f7a56-279">예를 들어를 실행 하면 `dotnet user-secrets remove "Movies:ConnectionString"` `Movies` 개체 리터럴이 축소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="f7a56-280">수정 된 파일은 다음 JSON과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="f7a56-281">여러 비밀 설정</span><span class="sxs-lookup"><span data-stu-id="f7a56-281">Set multiple secrets</span></span>

<span data-ttu-id="f7a56-282">암호 일괄 처리는 명령에 대해 파이프로 파이프 하 여 설정할 수 있습니다 `set` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="f7a56-283">다음 예제에서는 파일의 내용 *에 대 한input.js* 를 명령으로 파이프 `set` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="f7a56-284">Windows</span><span class="sxs-lookup"><span data-stu-id="f7a56-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="f7a56-285">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="f7a56-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="f7a56-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="f7a56-287">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="f7a56-288">비밀 액세스</span><span class="sxs-lookup"><span data-stu-id="f7a56-288">Access a secret</span></span>

<span data-ttu-id="f7a56-289">[구성 API](xref:fundamentals/configuration/index) 는 사용자 암호에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="f7a56-290">프로젝트가 .NET Framework 대상으로 하는 경우 [Microsoft.Extensions.Configuration를 설치 합니다. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="f7a56-291">ASP.NET Core 2.0 이상에서는 프로젝트가를 호출할 때 사용자 암호 구성 소스가 개발 모드에서 자동으로 추가 됩니다 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="f7a56-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="f7a56-292">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>가 인 경우를 호출 합니다 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> .</span><span class="sxs-lookup"><span data-stu-id="f7a56-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="f7a56-293">`CreateDefaultBuilder`가 호출 되지 않은 경우 생성자에서를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> `Startup` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="f7a56-294">`AddUserSecrets`다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="f7a56-295">사용자 암호는 .NET 구성 API를 통해 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="f7a56-296">POCO에 비밀 매핑</span><span class="sxs-lookup"><span data-stu-id="f7a56-296">Map secrets to a POCO</span></span>

<span data-ttu-id="f7a56-297">전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-298">위의 비밀을 POCO에 매핑하려면 .NET 구성 API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="f7a56-299">다음 코드는 사용자 지정 POCO에 바인딩하고 `MovieSettings` 속성 값에 액세스 합니다 `ServiceApiKey` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="f7a56-300">`Movies:ConnectionString`및 `Movies:ServiceApiKey` 암호는의 해당 속성에 매핑됩니다 `MovieSettings` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="f7a56-301">암호를 사용 하 여 문자열 대체</span><span class="sxs-lookup"><span data-stu-id="f7a56-301">String replacement with secrets</span></span>

<span data-ttu-id="f7a56-302">암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="f7a56-303">예를 들어에 저장 된 데이터베이스 연결 문자열은 *appsettings.json* 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="f7a56-304">보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="f7a56-305">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="f7a56-306">`Password`에서 연결 문자열의 키-값 쌍을 제거 합니다 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="f7a56-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="f7a56-307">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="f7a56-308">개체의 속성에 대해 암호의 값을 설정 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 하 여 연결 문자열을 완성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="f7a56-309">암호 나열</span><span class="sxs-lookup"><span data-stu-id="f7a56-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-310">프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="f7a56-311">다음과 같은 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="f7a56-312">앞의 예제에서 키 이름의 콜론은 *secrets.js에서* 개체 계층 구조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="f7a56-313">단일 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="f7a56-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-314">프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="f7a56-315">키에 연결 된 키-값 쌍을 제거 하기 위해 파일 *에 대 한 앱secrets.js* 수정 되었습니다 `MoviesConnectionString` .</span><span class="sxs-lookup"><span data-stu-id="f7a56-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="f7a56-316">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="f7a56-317">모든 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="f7a56-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="f7a56-318">프로젝트 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="f7a56-319">앱에 대 한 모든 사용자 비밀이 파일 *의secrets.js* 에서 삭제 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="f7a56-320">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f7a56-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="f7a56-321">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="f7a56-321">Additional resources</span></span>

* <span data-ttu-id="f7a56-322">IIS에서 사용자 암호에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f7a56-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end