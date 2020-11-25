---
title: ASP.NET Core의 구성
author: rick-anderson
description: 구성 API를 사용하여 ASP.NET Core 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: c04dcc65f7518d2d8b32cdce7a7fbb756dd8ec3a
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417541"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="52d50-103">ASP.NET Core의 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="52d50-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="52d50-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52d50-105">ASP.NET Core에서 구성은 하나 이상의 [구성 공급자](#cp)를 사용하여 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="52d50-106">구성 공급자는 다음과 같은 다양한 구성 소스를 사용하여 키-값 쌍에서 구성 데이터를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="52d50-107">설정 파일(예: *appsettings.json* )</span><span class="sxs-lookup"><span data-stu-id="52d50-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="52d50-108">환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-108">Environment variables</span></span>
* <span data-ttu-id="52d50-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52d50-109">Azure Key Vault</span></span>
* <span data-ttu-id="52d50-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="52d50-110">Azure App Configuration</span></span>
* <span data-ttu-id="52d50-111">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-111">Command-line arguments</span></span>
* <span data-ttu-id="52d50-112">설치되거나 만들어진 사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="52d50-113">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-113">Directory files</span></span>
* <span data-ttu-id="52d50-114">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="52d50-114">In-memory .NET objects</span></span>

<span data-ttu-id="52d50-115">이 항목에서는 ASP.NET Core의 구성에 대한 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="52d50-116">콘솔 앱에서 구성을 사용하는 방법에 대한 자세한 내용은 [.NET 구성](/dotnet/core/extensions/configuration)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="52d50-117">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52d50-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="52d50-118">기본 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-118">Default configuration</span></span>

<span data-ttu-id="52d50-119">[dotnet new](/dotnet/core/tools/dotnet-new) 또는 Visual Studio를 사용하여 만든 ASP.NET Core 웹앱은 다음 코드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="52d50-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="52d50-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  기존 `IConfiguration`을 소스로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="52d50-122">기본 구성 사례에서는 [호스트](#hvac) 구성을 추가하고 앱 구성의 첫 번째 소스로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="52d50-123">[JSON 구성 공급자](#file-configuration-provider)를 사용하는 [appsettings.json](#appsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="52d50-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="52d50-124">[JSON 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="52d50-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="52d50-125">예: *appsettings*.***Production\*\*_._json* 및 *appsettings*.\*\*\*Development** _._json\*.</span><span class="sxs-lookup"><span data-stu-id="52d50-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="52d50-126">앱이 `Development` 환경에서 실행되는 경우 [앱 비밀](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="52d50-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="52d50-127">[환경 변수 구성 공급자](#evcp)를 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="52d50-128">[명령줄 구성 공급자](#command-line)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="52d50-129">나중에 추가된 구성 공급자는 이전 키 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="52d50-130">예를 들어 `MyKey`가 *appsettings.json* 과 환경 모두에서 설정된 경우 환경 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="52d50-131">기본 구성 공급자를 사용하여 [명령줄 구성 공급자](#clcp)는 다른 모든 공급자를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="52d50-132">`CreateDefaultBuilder`에 대한 자세한 내용은 [기본 작성기 설정](xref:fundamentals/host/generic-host#default-builder-settings)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="52d50-133">다음 코드는 추가된 순서대로 사용하도록 설정된 구성 공급자를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="52d50-134">다음 *appsettings.json* 파일을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="52d50-135">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-136">기본 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 다음과 같은 순서로 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="52d50-137">*appsettings.* `Environment` *.json*: 예: *appsettings*.***Production\*\*_._json* 및 *appsettings*.\*\*\*Development** _._json\* 파일.</span><span class="sxs-lookup"><span data-stu-id="52d50-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="52d50-138">파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="52d50-139">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="52d50-140">*appsettings*.`Environment`.*json* 값은 *appsettings.json* 의 키를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="52d50-141">예를 들어 기본적으로 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-141">For example, by default:</span></span>

* <span data-ttu-id="52d50-142">개발 환경에서는 *appsettings*.***Development** _._json* 구성이 *appsettings.json* 에서 찾은 값을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="52d50-143">프로덕션 환경에서는 *appsettings*.***Production** _._json* 구성이 *appsettings.json* 에서 찾은 값을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="52d50-144">Azure에 앱을 배포하는 경우를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="52d50-145">옵션 패턴을 사용하여 계층적 구성 데이터 바인딩</span><span class="sxs-lookup"><span data-stu-id="52d50-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="52d50-146">[기본](#default) 구성을 사용하여 *appsettings.json* 및 *appsettings.* `Environment` *.json* 파일은 [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75)를 통해 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="52d50-147">앱이 시작된 **이후** *appsettings.json* 및 *appsettings.* `Environment` *.json* 파일에 대한 변경 내용은 [JSON 구성 공급자](#jcp)에서 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file \***after** _ the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="52d50-148">추가 JSON 구성 파일 추가에 대한 자세한 내용은 이 문서의 [JSON 구성 공급자](#jcp)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="52d50-149">서비스 컬렉션 결합</span><span class="sxs-lookup"><span data-stu-id="52d50-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="52d50-150">보안 및 비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="52d50-150">Security and secret manager</span></span>

<span data-ttu-id="52d50-151">구성 데이터 지침:</span><span class="sxs-lookup"><span data-stu-id="52d50-151">Configuration data guidelines:</span></span>

<span data-ttu-id="52d50-152">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-152">_ Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="52d50-153">[비밀 관리자](xref:security/app-secrets)를 사용하여 개발에 사용되는 비밀을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="52d50-154">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="52d50-155">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="52d50-156">[기본](#default)적으로, [비밀 관리자](xref:security/app-secrets)는 *appsettings.json* 및 *appsettings.* `Environment` *.json* 뒤에 구성 설정을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="52d50-157">암호 또는 기타 중요한 데이터 저장에 대한 자세한 정보:</span><span class="sxs-lookup"><span data-stu-id="52d50-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="52d50-158"><xref:security/app-secrets>:  환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="52d50-159">비밀 관리자는 [파일 구성 공급자](#fcp)를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="52d50-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="52d50-161">자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="52d50-162">환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-162">Environment variables</span></span>

<span data-ttu-id="52d50-163">[기본](#default) 구성을 사용하여 <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 *appsettings.json* , *appsettings.* `Environment` *.json*, [비밀 관리자](xref:security/app-secrets)를 읽은 후 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="52d50-164">따라서 환경에서 읽은 키 값이 *appsettings.json* , *appsettings.* `Environment` *.json*, 비밀 관리자에서 읽은 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="52d50-165">다음 `set` 명령은,</span><span class="sxs-lookup"><span data-stu-id="52d50-165">The following `set` commands:</span></span>

* <span data-ttu-id="52d50-166">Windows에서 [위의 예제](#appsettingsjson)에 나오는 환경 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="52d50-167">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)를 사용하는 경우 설정을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="52d50-168">`dotnet run` 명령은 프로젝트 디렉터리에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="52d50-169">위의 환경 설정은,</span><span class="sxs-lookup"><span data-stu-id="52d50-169">The preceding environment settings:</span></span>

* <span data-ttu-id="52d50-170">설정된 명령 창에서 시작된 프로세스에서만 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="52d50-171">Visual Studio에서 시작된 브라우저에서는 읽을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="52d50-172">다음 [setx](/windows-server/administration/windows-commands/setx) 명령을 사용하여 Windows에서 환경 키 및 값을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="52d50-173">`set`와 달리, `setx` 설정은 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="52d50-174">`/M`은 시스템 환경에서 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="52d50-175">`/M` 스위치를 사용하지 않으면 사용자 환경 변수가 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="52d50-176">위의 명령이 *appsettings.json* 및 *appsettings.* `Environment` *.json* 을 재정의하는지 테스트하려면:</span><span class="sxs-lookup"><span data-stu-id="52d50-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="52d50-177">Visual Studio를 사용하는 경우: Visual Studio를 종료했다가 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="52d50-178">CLI를 사용하는 경우: 새 명령 창을 시작하고 `dotnet run`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="52d50-179">환경 변수의 접두사를 지정하는 문자열을 사용하여 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="52d50-180">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="52d50-180">In the preceding code:</span></span>

* <span data-ttu-id="52d50-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")`는 [기본 구성 공급자](#default) 뒤에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="52d50-182">구성 공급자 순서 지정 예제는 [JSON 구성 공급자](#jcp)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="52d50-183">`MyCustomPrefix_` 접두사로 설정된 환경 변수는 [기본 구성 공급자](#default)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="52d50-184">여기에는 접두사 없는 환경 변수가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="52d50-185">구성 키-값 쌍을 읽으면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="52d50-186">다음 명령은 사용자 지정 접두사를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="52d50-187">[기본 구성](#default)은 `DOTNET_` 및 `ASPNETCORE_` 접두사가 붙은 환경 변수 및 명령줄 인수를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="52d50-188">`DOTNET_` 및 `ASPNETCORE_` 접두사는 ASP.NET Core에서 [호스트 및 앱 구성](xref:fundamentals/host/generic-host#host-configuration)에 사용되지만, 사용자 구성에는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="52d50-189">호스트 및 앱 구성에 대한 자세한 내용은 [.NET 제네릭 호스트](xref:fundamentals/host/generic-host)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="52d50-190">[Azure App Service](https://azure.microsoft.com/services/app-service/)의 **설정 > 구성** 페이지에서 **새 애플리케이션 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="52d50-191">Azure App Service 애플리케이션 설정은,</span><span class="sxs-lookup"><span data-stu-id="52d50-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="52d50-192">미사용 시 암호화되고 암호화된 채널을 통해 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="52d50-193">환경 변수로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-193">Exposed as environment variables.</span></span>

<span data-ttu-id="52d50-194">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="52d50-195">Azure 데이터베이스 연결 문자열에 대한 자세한 내용은 [연결 문자열 접두사](#constr)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="52d50-196">환경 변수 명명</span><span class="sxs-lookup"><span data-stu-id="52d50-196">Naming of environment variables</span></span>

<span data-ttu-id="52d50-197">환경 변수의 이름은 *appsettings.json* 파일의 구조를 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-197">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="52d50-198">계층 구조의 각 요소는 이중 밑줄(권장) 또는 콜론으로 구분됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-198">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="52d50-199">요소 구조에 배열이 포함된 경우, 해당 배열 인덱스는 이 경로의 추가 요소 이름으로 처리되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-199">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="52d50-200">다음 *appsettings.json* 파일 및 환경 변수라고 표시된 해당 값을 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-200">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="52d50-201">**환경 변수**</span><span class="sxs-lookup"><span data-stu-id="52d50-201">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="52d50-202">launchSettings.json에 설정된 환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-202">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="52d50-203">*launchSettings.json* 에 설정된 환경 변수는 시스템 환경에 설정된 변수를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-203">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="52d50-204">명령줄</span><span class="sxs-lookup"><span data-stu-id="52d50-204">Command-line</span></span>

<span data-ttu-id="52d50-205">[기본](#default) 구성을 사용하여 <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>는 다음 구성 소스 뒤에 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-205">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="52d50-206">*appsettings.json* 및 *appsettings*.`Environment`.*json* 파일.</span><span class="sxs-lookup"><span data-stu-id="52d50-206">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="52d50-207">개발 환경의 [앱 비밀(비밀 관리자)](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="52d50-207">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52d50-208">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="52d50-208">Environment variables.</span></span>

<span data-ttu-id="52d50-209">[기본](#default)적으로, 명령줄에 설정된 구성 값은 다른 모든 구성 공급자를 사용하여 설정된 구성 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-209">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="52d50-210">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-210">Command-line arguments</span></span>

<span data-ttu-id="52d50-211">다음 명령은 `=`을 사용하여 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-211">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="52d50-212">다음 명령은 `/`를 사용하여 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-212">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="52d50-213">다음 명령은 `--`를 사용하여 키 및 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-213">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="52d50-214">키 값은,</span><span class="sxs-lookup"><span data-stu-id="52d50-214">The key value:</span></span>

* <span data-ttu-id="52d50-215">`=` 다음에 와야 합니다. 또는 값이 공백에 다음에 오는 경우 키에 `--` 또는 `/` 접두사가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-215">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="52d50-216">`=`이 사용된 경우 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-216">Isn't required if `=` is used.</span></span> <span data-ttu-id="52d50-217">예: `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="52d50-217">For example, `MySetting=`.</span></span>

<span data-ttu-id="52d50-218">같은 명령 내에서 `=`을 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-218">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="52d50-219">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="52d50-219">Switch mappings</span></span>

<span data-ttu-id="52d50-220">스위치 매핑은 **키** 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-220">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="52d50-221"><xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 스위치 교체 사전을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-221">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="52d50-222">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-222">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="52d50-223">사전에서 명령줄 키가 발견되면 사전 값이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-223">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="52d50-224">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-224">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="52d50-225">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="52d50-225">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="52d50-226">스위치는 `-` 또는 `--`으로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-226">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="52d50-227">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-227">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="52d50-228">스위치 매핑 사전을 사용하려면 `AddCommandLine`에 대한 호출에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-228">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="52d50-229">다음 코드는 교체된 키의 키 값을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-229">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-230">다음 명령은 작동하여 키 교체를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-230">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="52d50-231">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-231">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="52d50-232">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-232">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52d50-233">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-233">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="52d50-234">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="52d50-234">Hierarchical configuration data</span></span>

<span data-ttu-id="52d50-235">구성 API는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-235">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="52d50-236">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *appsettings.json* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-236">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="52d50-237">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-237">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-238">계층적 구성 데이터를 읽는 기본 방법은 옵션 패턴을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-238">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="52d50-239">자세한 내용은 이 문서의 [계층적 구성 데이터 바인딩](#optpat)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-239">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="52d50-240">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-240"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="52d50-241">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-241">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="52d50-242">구성 키 및 값</span><span class="sxs-lookup"><span data-stu-id="52d50-242">Configuration keys and values</span></span>

<span data-ttu-id="52d50-243">구성 키는,</span><span class="sxs-lookup"><span data-stu-id="52d50-243">Configuration keys:</span></span>

* <span data-ttu-id="52d50-244">대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-244">Are case-insensitive.</span></span> <span data-ttu-id="52d50-245">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-245">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="52d50-246">두 개 이상의 구성 공급자에 키와 값이 설정되어 있으면 추가된 마지막 공급자의 값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-246">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="52d50-247">자세한 내용은 [기본 구성](#default)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-247">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="52d50-248">계층적 키</span><span class="sxs-lookup"><span data-stu-id="52d50-248">Hierarchical keys</span></span>
  * <span data-ttu-id="52d50-249">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-249">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="52d50-250">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-250">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="52d50-251">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론(`:`)으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-251">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="52d50-252">Azure Key Vault에서 계층적 키는 `--`를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-252">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="52d50-253">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 암호를 앱의 구성으로 로드할 때 `--`를 자동으로 `:`으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-253">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="52d50-254"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-254">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52d50-255">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#boa) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-255">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="52d50-256">구성 값은,</span><span class="sxs-lookup"><span data-stu-id="52d50-256">Configuration values:</span></span>

* <span data-ttu-id="52d50-257">문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-257">Are strings.</span></span>
* <span data-ttu-id="52d50-258">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-258">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="52d50-259">구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-259">Configuration providers</span></span>

<span data-ttu-id="52d50-260">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-260">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="52d50-261">공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-261">Provider</span></span> | <span data-ttu-id="52d50-262">다음에서 구성 제공</span><span class="sxs-lookup"><span data-stu-id="52d50-262">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="52d50-263">Azure Key Vault 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-263">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="52d50-264">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52d50-264">Azure Key Vault</span></span> |
| [<span data-ttu-id="52d50-265">Azure 앱 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-265">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="52d50-266">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="52d50-266">Azure App Configuration</span></span> |
| [<span data-ttu-id="52d50-267">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-267">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="52d50-268">명령줄 매개 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-268">Command-line parameters</span></span> |
| [<span data-ttu-id="52d50-269">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-269">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="52d50-270">사용자 지정 소스</span><span class="sxs-lookup"><span data-stu-id="52d50-270">Custom source</span></span> |
| [<span data-ttu-id="52d50-271">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-271">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="52d50-272">환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-272">Environment variables</span></span> |
| [<span data-ttu-id="52d50-273">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-273">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="52d50-274">INI, JSON 및 XML 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-274">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="52d50-275">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-275">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="52d50-276">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-276">Directory files</span></span> |
| [<span data-ttu-id="52d50-277">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-277">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="52d50-278">메모리 내 컬렉션</span><span class="sxs-lookup"><span data-stu-id="52d50-278">In-memory collections</span></span> |
| [<span data-ttu-id="52d50-279">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="52d50-279">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="52d50-280">사용자 프로필 디렉터리의 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-280">File in the user profile directory</span></span> |

<span data-ttu-id="52d50-281">구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-281">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="52d50-282">앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-282">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="52d50-283">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-283">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="52d50-284">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="52d50-284">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="52d50-285">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="52d50-285">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="52d50-286">[환경 변수 구성 공급자](#evcp)를 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-286">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="52d50-287">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-287">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="52d50-288">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 추가하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-288">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="52d50-289">위의 공급자 시퀀스는 [기본 구성](#default)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-289">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="52d50-290">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="52d50-290">Connection string prefixes</span></span>

<span data-ttu-id="52d50-291">구성 API에는 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-291">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="52d50-292">해당 연결 문자열은 앱 환경의 Azure 연결 문자열을 구성하는 데 관련됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-292">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="52d50-293">[기본 구성](#default)을 사용하거나 `AddEnvironmentVariables`에 제공된 접두사가 없는 경우 표에 표시된 접두사가 붙은 환경 변수가 앱에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-293">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="52d50-294">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="52d50-294">Connection string prefix</span></span> | <span data-ttu-id="52d50-295">공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-295">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="52d50-296">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-296">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="52d50-297">MySQL</span><span class="sxs-lookup"><span data-stu-id="52d50-297">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="52d50-298">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="52d50-298">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="52d50-299">SQL Server</span><span class="sxs-lookup"><span data-stu-id="52d50-299">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="52d50-300">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-300">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="52d50-301">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-301">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="52d50-302">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-302">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="52d50-303">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="52d50-303">Environment variable key</span></span> | <span data-ttu-id="52d50-304">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="52d50-304">Converted configuration key</span></span> | <span data-ttu-id="52d50-305">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="52d50-305">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-306">구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-306">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-307">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="52d50-307">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52d50-308">값: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="52d50-308">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-309">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="52d50-309">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52d50-310">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="52d50-310">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-311">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="52d50-311">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52d50-312">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="52d50-312">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="52d50-313">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-313">File configuration provider</span></span>

<span data-ttu-id="52d50-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-314"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="52d50-315">다음 구성 공급자는 `FileConfigurationProvider`에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-315">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="52d50-316">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-316">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="52d50-317">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-317">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="52d50-318">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-318">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="52d50-319">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-319">INI configuration provider</span></span>

<span data-ttu-id="52d50-320"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-320">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="52d50-321">다음 코드는 모든 구성 공급자를 지우고 여러 구성 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-321">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="52d50-322">위의 코드에서 *MyIniConfig.ini* 및 *MyIniConfig*.`Environment`.*ini* 파일의 설정은 다음의 설정에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-322">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="52d50-323">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-323">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="52d50-324">[명령줄 구성 공급자](#clcp)</span><span class="sxs-lookup"><span data-stu-id="52d50-324">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52d50-325">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *MyIniConfig.ini* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-325">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="52d50-326">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-326">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="52d50-327">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-327">JSON configuration provider</span></span>

<span data-ttu-id="52d50-328"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-328">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="52d50-329">오버로드는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-329">Overloads can specify:</span></span>

* <span data-ttu-id="52d50-330">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-330">Whether the file is optional.</span></span>
* <span data-ttu-id="52d50-331">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-331">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="52d50-332">다음 코드를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-332">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="52d50-333">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="52d50-333">The preceding code:</span></span>

* <span data-ttu-id="52d50-334">다음 옵션을 사용하여 *MyConfig.json* 파일을 로드하도록 JSON 구성 공급자를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-334">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="52d50-335">`optional: true`: 파일은 선택 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-335">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="52d50-336">`reloadOnChange: true`은: 변경 내용이 저장되면 파일이 다시 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-336">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="52d50-337">*MyConfig.json* 파일 전에 [기본 구성 공급자](#default)를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-337">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="52d50-338">*MyConfig.json* 파일의 설정은 [환경 변수 구성 공급자](#evcp) 및 [명령줄 구성 공급자](#clcp)를 비롯한 기본 구성 공급자의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-338">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52d50-339">일반적으로 사용자 지정 JSON 파일이 [환경 변수 구성 공급자](#evcp) 및 [명령줄 구성 공급자](#clcp)에 설정된 값을 재정의하기를 원치 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="52d50-339">You typically \***don't** _ want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52d50-340">다음 코드는 모든 구성 공급자를 지우고 여러 구성 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-340">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="52d50-341">위의 코드에서 _MyConfig.json\* 및 *MyConfig*.`Environment`.*json* 파일의 설정은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-341">In the preceding code, settings in the _MyConfig.json\* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="52d50-342">*appsettings.json* 및 *appsettings*.`Environment`.*json* 파일의 설정을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-342">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="52d50-343">[환경 변수 구성 공급자](#evcp) 및 [명령줄 구성 공급자](#clcp)의 설정에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-343">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52d50-344">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *MyConfig.json* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-344">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="52d50-345">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-345">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="52d50-346">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-346">XML configuration provider</span></span>

<span data-ttu-id="52d50-347"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-347">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="52d50-348">다음 코드는 모든 구성 공급자를 지우고 여러 구성 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-348">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="52d50-349">위의 코드에서 *MyXMLFile.xml* 및 *MyXMLFile*.`Environment`.*xml* 파일의 설정은 다음의 설정에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-349">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="52d50-350">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-350">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="52d50-351">[명령줄 구성 공급자](#clcp)</span><span class="sxs-lookup"><span data-stu-id="52d50-351">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="52d50-352">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)에는 다음 *MyXMLFile.xml* 파일이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-352">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="52d50-353">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 몇 가지 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-353">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-354">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-354">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="52d50-355">다음 코드는 이전 구성 파일을 읽고 키 및 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-355">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-356">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-356">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="52d50-357">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-357">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52d50-358">key:attribute</span><span class="sxs-lookup"><span data-stu-id="52d50-358">key:attribute</span></span>
* <span data-ttu-id="52d50-359">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="52d50-359">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="52d50-360">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-360">Key-per-file configuration provider</span></span>

<span data-ttu-id="52d50-361"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-361">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="52d50-362">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="52d50-362">The key is the file name.</span></span> <span data-ttu-id="52d50-363">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-363">The value contains the file's contents.</span></span> <span data-ttu-id="52d50-364">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-364">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="52d50-365">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-365">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="52d50-366">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-366">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="52d50-367">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-367">Overloads permit specifying:</span></span>

* <span data-ttu-id="52d50-368">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="52d50-368">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="52d50-369">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="52d50-369">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="52d50-370">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-370">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="52d50-371">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-371">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="52d50-372">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-372">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="52d50-373">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-373">Memory configuration provider</span></span>

<span data-ttu-id="52d50-374"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-374">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="52d50-375">다음 코드는 구성 시스템에 메모리 컬렉션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-375">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="52d50-376">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 다음 코드는 위의 구성 설정을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-376">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-377">위의 코드에서 `config.AddInMemoryCollection(Dict)`는 [기본 구성 공급자](#default) 뒤에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-377">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="52d50-378">구성 공급자 순서 지정 예제는 [JSON 구성 공급자](#jcp)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-378">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="52d50-379">`MemoryConfigurationProvider`를 사용하는 또 다른 예제는 [배열 바인딩](#boa)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-379">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="52d50-380">GetValue</span><span class="sxs-lookup"><span data-stu-id="52d50-380">GetValue</span></span>

<span data-ttu-id="52d50-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 단일 값을 추출하고 해당 값을 지정된 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-381">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-382">위의 코드에서 구성에 `NumberKey`가 없는 경우 기본값 `99`가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-382">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="52d50-383">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="52d50-383">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="52d50-384">이어지는 예제에서는 다음 *MySubsection.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-384">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="52d50-385">다음 코드는 구성 공급자에 *MySubsection.json* 을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-385">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="52d50-386">GetSection</span><span class="sxs-lookup"><span data-stu-id="52d50-386">GetSection</span></span>

<span data-ttu-id="52d50-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-387">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="52d50-388">다음 코드는 `section1`의 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-388">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-389">다음 코드는 `section2:subsection0`의 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-389">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-390">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-390">`GetSection` never returns `null`.</span></span> <span data-ttu-id="52d50-391">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-391">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="52d50-392">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-392">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="52d50-393">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-393">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="52d50-394">GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="52d50-394">GetChildren and Exists</span></span>

<span data-ttu-id="52d50-395">다음 코드는 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하고 `section2:subsection0`의 값을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-395">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-396">위의 코드는 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 호출하여 섹션이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-396">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="52d50-397">배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="52d50-397">Bind an array</span></span>

<span data-ttu-id="52d50-398">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*)는 구성 키에서 배열 인덱스를 사용하여 배열을 개체에 바인딩할 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-398">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52d50-399">숫자 키 세그먼트를 노출하는 모든 배열 형식은 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 클래스 배열에 배열을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-399">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="52d50-400">[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)의 *MyArray.json* 을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-400">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="52d50-401">다음 코드는 구성 공급자에 *MyArray.json* 을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-401">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="52d50-402">다음 코드는 구성을 읽고 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-402">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-403">위의 코드는 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-403">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="52d50-404">위의 출력에서 Index 3은 *MyArray.json* 의 `"4": "value40",`에 해당하는 `value40` 값을 가집니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-404">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="52d50-405">바인딩된 배열 인덱스는 연속되며 구성 키 인덱스에 바인딩되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-405">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="52d50-406">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-406">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="52d50-407">다음 코드는 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 사용하여 `array:entries` 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-407">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="52d50-408">다음 코드는 `arrayDict` `Dictionary`의 구성을 읽고 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-408">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-409">위의 코드는 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-409">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="52d50-410">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-410">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="52d50-411">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-411">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="52d50-412">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-412">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="52d50-413">Index &num;3에 대한 누락된 구성 항목은 Index &num;3 키/값 쌍을 읽는 모든 구성 공급자에서 `ArrayExample` 인스턴스에 바인딩하기 전에 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-413">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="52d50-414">샘플 다운로드의 다음 *Value3.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-414">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="52d50-415">다음 코드에는 *Value3.json* 및 `arrayDict` `Dictionary`의 구성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-415">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="52d50-416">다음 코드는 위의 구성을 읽고 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-416">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-417">위의 코드는 다음 출력을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-417">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="52d50-418">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-418">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="52d50-419">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-419">Custom configuration provider</span></span>

<span data-ttu-id="52d50-420">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-420">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="52d50-421">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-421">The provider has the following characteristics:</span></span>

* <span data-ttu-id="52d50-422">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-422">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="52d50-423">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-423">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="52d50-424">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-424">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="52d50-425">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-425">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="52d50-426">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-426">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="52d50-427">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-427">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="52d50-428">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-428">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="52d50-429">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-429">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="52d50-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-430">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="52d50-431"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-431">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="52d50-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-432">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="52d50-433"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-433">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="52d50-434">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-434">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="52d50-435">[구성 키는 대/소문자를](#keys)구분하지 않으므로 데이터베이스를 초기화하는 데 사용되는 사전은 대/소문자를 구분하지 않는 비교자([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase))를 사용하여 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-435">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="52d50-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-436">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="52d50-437">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-437">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="52d50-438">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-438">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="52d50-439">다음 코드는 *Program.cs* 에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-439">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="52d50-440">시작 시 구성 액세스</span><span class="sxs-lookup"><span data-stu-id="52d50-440">Access configuration in Startup</span></span>

<span data-ttu-id="52d50-441">다음 코드는 `Startup` 메서드의 구성 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-441">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="52d50-442">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-442">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="52d50-443">Razor Pages의 구성 액세스</span><span class="sxs-lookup"><span data-stu-id="52d50-443">Access configuration in Razor Pages</span></span>

<span data-ttu-id="52d50-444">다음 코드는 Razor 페이지의 구성 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-444">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="52d50-445">다음 코드에서 `MyOptions`는 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*>를 통해 서비스 컨테이너에 추가되고 구성에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-445">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="52d50-446">다음 표시는 [`@inject`](xref:mvc/views/razor#inject) Razor 지시문을 사용하여 옵션 값을 확인하고 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-446">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="52d50-447">MVC 뷰 파일의 구성 액세스</span><span class="sxs-lookup"><span data-stu-id="52d50-447">Access configuration in a MVC view file</span></span>

<span data-ttu-id="52d50-448">다음 코드는 MVC 뷰의 구성 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-448">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="52d50-449">대리자로 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-449">Configure options with a delegate</span></span>

<span data-ttu-id="52d50-450">대리자에서 구성된 옵션은 구성 공급자에 설정된 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-450">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="52d50-451">대리자를 사용한 옵션 구성은 샘플 앱에 예제 2로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-451">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="52d50-452">다음 코드에서 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 서비스가 서비스 컨테이너에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-452">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="52d50-453">`MyOptions`의 값을 구성하는 데 대리자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-453">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="52d50-454">다음 코드는 다음과 같은 옵션 값을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-454">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="52d50-455">위의 예제에서 `Option1` 및 `Option2`의 값은 모두 *appsettings.json* 에서 지정되고 구성된 대리자에 의해 재정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-455">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="52d50-456">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-456">Host versus app configuration</span></span>

<span data-ttu-id="52d50-457">앱을 구성하고 시작하기 전에 *호스트* 를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-457">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="52d50-458">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-458">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="52d50-459">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-459">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="52d50-460">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-460">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="52d50-461">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-461">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="52d50-462">기본 호스트 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-462">Default host configuration</span></span>

<span data-ttu-id="52d50-463">[웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 기본 구성에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.2 버전](?view=aspnetcore-2.2)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-463">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="52d50-464">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-464">Host configuration is provided from:</span></span>
  * <span data-ttu-id="52d50-465">[환경 변수 구성 공급자](#environment-variables)를 사용하는 `DOTNET_` 접두사가 붙은 환경 변수(예: `DOTNET_ENVIRONMENT`).</span><span class="sxs-lookup"><span data-stu-id="52d50-465">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="52d50-466">구성 키-값 쌍이 로드되면 접두사(`DOTNET_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-466">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="52d50-467">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-467">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="52d50-468">웹 호스트 기본 구성이 설정됩니다(`ConfigureWebHostDefaults`).</span><span class="sxs-lookup"><span data-stu-id="52d50-468">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="52d50-469">Kestrel은 웹 서버로 사용되며 앱의 구성 공급자를 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-469">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="52d50-470">호스트 필터링 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-470">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="52d50-471">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 전달된 헤더 미들웨어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-471">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="52d50-472">IIS 통합을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-472">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="52d50-473">기타 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-473">Other configuration</span></span>

<span data-ttu-id="52d50-474">이 항목에서는 *앱 구성* 에 관련된 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-474">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="52d50-475">ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-475">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="52d50-476">*launch.json*/*launchSettings.json* 은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-476">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="52d50-477"><xref:fundamentals/environments#development> 문서 내</span><span class="sxs-lookup"><span data-stu-id="52d50-477">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="52d50-478">개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체</span><span class="sxs-lookup"><span data-stu-id="52d50-478">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="52d50-479">*web.config* 는 다음 항목에 설명된 서버 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-479">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="52d50-480">*launchSettings.json* 에 설정된 환경 변수는 시스템 환경에 설정된 변수를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-480">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="52d50-481">이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-481">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="52d50-482">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="52d50-482">Add configuration from an external assembly</span></span>

<span data-ttu-id="52d50-483"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-483">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="52d50-484">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-484">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52d50-485">추가 자료</span><span class="sxs-lookup"><span data-stu-id="52d50-485">Additional resources</span></span>

* [<span data-ttu-id="52d50-486">구성 소스 코드</span><span class="sxs-lookup"><span data-stu-id="52d50-486">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52d50-487">ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-487">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="52d50-488">구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-488">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="52d50-489">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52d50-489">Azure Key Vault</span></span>
* <span data-ttu-id="52d50-490">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="52d50-490">Azure App Configuration</span></span>
* <span data-ttu-id="52d50-491">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-491">Command-line arguments</span></span>
* <span data-ttu-id="52d50-492">사용자 지정 공급자(설치 또는 생성된)</span><span class="sxs-lookup"><span data-stu-id="52d50-492">Custom providers (installed or created)</span></span>
* <span data-ttu-id="52d50-493">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-493">Directory files</span></span>
* <span data-ttu-id="52d50-494">환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-494">Environment variables</span></span>
* <span data-ttu-id="52d50-495">메모리 내 .NET 개체</span><span class="sxs-lookup"><span data-stu-id="52d50-495">In-memory .NET objects</span></span>
* <span data-ttu-id="52d50-496">설정 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-496">Settings files</span></span>

<span data-ttu-id="52d50-497">일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-497">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="52d50-498">샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:</span><span class="sxs-lookup"><span data-stu-id="52d50-498">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="52d50-499">‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-499">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="52d50-500">옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-500">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="52d50-501">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-501">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="52d50-502">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52d50-502">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="52d50-503">호스트 대 앱 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-503">Host versus app configuration</span></span>

<span data-ttu-id="52d50-504">앱을 구성하고 시작하기 전에 *호스트* 를 구성하고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-504">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="52d50-505">호스트는 앱 시작 및 수명 관리를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-505">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="52d50-506">앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-506">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="52d50-507">호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-507">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="52d50-508">호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-508">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="52d50-509">기타 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-509">Other configuration</span></span>

<span data-ttu-id="52d50-510">이 항목에서는 *앱 구성* 에 관련된 내용만 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-510">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="52d50-511">ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-511">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="52d50-512">*launch.json*/*launchSettings.json* 은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-512">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="52d50-513"><xref:fundamentals/environments#development> 문서 내</span><span class="sxs-lookup"><span data-stu-id="52d50-513">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="52d50-514">개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체</span><span class="sxs-lookup"><span data-stu-id="52d50-514">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="52d50-515">*web.config* 는 다음 항목에 설명된 서버 구성 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-515">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="52d50-516">이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-516">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="52d50-517">기본 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-517">Default configuration</span></span>

<span data-ttu-id="52d50-518">호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-518">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="52d50-519">`CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-519">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="52d50-520">[웹호스트](xref:fundamentals/host/web-host)를 사용하는 앱에는 다음이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-520">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="52d50-521">[제네릭 호스트](xref:fundamentals/host/generic-host) 사용 시 기본 구성에 대한 자세한 내용은 [이 토픽의 최신 버전](xref:fundamentals/configuration/index)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-521">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="52d50-522">호스트 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-522">Host configuration is provided from:</span></span>
  * <span data-ttu-id="52d50-523">[환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `ASPNETCORE_`를 접두사로 사용하는 환경 변수(예: `ASPNETCORE_ENVIRONMENT`)</span><span class="sxs-lookup"><span data-stu-id="52d50-523">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="52d50-524">구성 키-값 쌍이 로드되면 접두사(`ASPNETCORE_`)는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-524">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="52d50-525">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-525">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="52d50-526">앱 구성은 다음에 의해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-526">App configuration is provided from:</span></span>
  * <span data-ttu-id="52d50-527">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="52d50-527">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="52d50-528">[파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*</span><span class="sxs-lookup"><span data-stu-id="52d50-528">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="52d50-529">앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-529">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="52d50-530">[환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-530">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="52d50-531">[명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-531">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="52d50-532">보안</span><span class="sxs-lookup"><span data-stu-id="52d50-532">Security</span></span>

<span data-ttu-id="52d50-533">중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-533">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="52d50-534">구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-534">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="52d50-535">개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-535">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="52d50-536">의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-536">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="52d50-537">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-537">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="52d50-538"><xref:security/app-secrets>: 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-538"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="52d50-539">비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-539">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="52d50-540">파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-540">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="52d50-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-541">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="52d50-542">자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-542">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="52d50-543">계층적 구성 데이터</span><span class="sxs-lookup"><span data-stu-id="52d50-543">Hierarchical configuration data</span></span>

<span data-ttu-id="52d50-544">구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-544">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="52d50-545">다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-545">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="52d50-546">파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-546">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="52d50-547">콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-547">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="52d50-548">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-548">section0:key0</span></span>
* <span data-ttu-id="52d50-549">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-549">section0:key1</span></span>
* <span data-ttu-id="52d50-550">section1:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-550">section1:key0</span></span>
* <span data-ttu-id="52d50-551">section1:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-551">section1:key1</span></span>

<span data-ttu-id="52d50-552">구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-552"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="52d50-553">이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-553">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="52d50-554">규칙</span><span class="sxs-lookup"><span data-stu-id="52d50-554">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="52d50-555">소스 및 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-555">Sources and providers</span></span>

<span data-ttu-id="52d50-556">앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-556">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="52d50-557">변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-557">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="52d50-558">예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-558">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="52d50-559"><xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-559"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="52d50-560"><xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-560"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="52d50-561">다음 예제에서는 `_config` 필드가 구성 값에 액세스하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-561">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="52d50-562">구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-562">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="52d50-563">키</span><span class="sxs-lookup"><span data-stu-id="52d50-563">Keys</span></span>

<span data-ttu-id="52d50-564">구성키는 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-564">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="52d50-565">키는 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-565">Keys are case-insensitive.</span></span> <span data-ttu-id="52d50-566">예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-566">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="52d50-567">같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-567">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="52d50-568">중복 JSON 키에 대한 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/extensions/issues/2381)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-568">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="52d50-569">계층적 키</span><span class="sxs-lookup"><span data-stu-id="52d50-569">Hierarchical keys</span></span>
  * <span data-ttu-id="52d50-570">구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-570">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="52d50-571">환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-571">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="52d50-572">두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-572">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="52d50-573">Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-573">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="52d50-574">비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 작성하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-574">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="52d50-575"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-575">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52d50-576">배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-576">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="52d50-577">값</span><span class="sxs-lookup"><span data-stu-id="52d50-577">Values</span></span>

<span data-ttu-id="52d50-578">구성 값은 다음 규칙을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-578">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="52d50-579">값은 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-579">Values are strings.</span></span>
* <span data-ttu-id="52d50-580">Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-580">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="52d50-581">공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-581">Providers</span></span>

<span data-ttu-id="52d50-582">다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-582">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="52d50-583">공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-583">Provider</span></span> | <span data-ttu-id="52d50-584">다음에서 구성 제공&hellip;</span><span class="sxs-lookup"><span data-stu-id="52d50-584">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="52d50-585">[Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목)</span><span class="sxs-lookup"><span data-stu-id="52d50-585">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="52d50-586">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52d50-586">Azure Key Vault</span></span> |
| <span data-ttu-id="52d50-587">[Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="52d50-587">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="52d50-588">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="52d50-588">Azure App Configuration</span></span> |
| [<span data-ttu-id="52d50-589">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-589">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="52d50-590">명령줄 매개 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-590">Command-line parameters</span></span> |
| [<span data-ttu-id="52d50-591">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-591">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="52d50-592">사용자 지정 소스</span><span class="sxs-lookup"><span data-stu-id="52d50-592">Custom source</span></span> |
| [<span data-ttu-id="52d50-593">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-593">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="52d50-594">환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-594">Environment variables</span></span> |
| [<span data-ttu-id="52d50-595">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-595">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="52d50-596">파일(INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="52d50-596">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="52d50-597">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-597">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="52d50-598">디렉터리 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-598">Directory files</span></span> |
| [<span data-ttu-id="52d50-599">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-599">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="52d50-600">메모리 내 컬렉션</span><span class="sxs-lookup"><span data-stu-id="52d50-600">In-memory collections</span></span> |
| <span data-ttu-id="52d50-601">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목)</span><span class="sxs-lookup"><span data-stu-id="52d50-601">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="52d50-602">사용자 프로필 디렉터리의 파일</span><span class="sxs-lookup"><span data-stu-id="52d50-602">File in the user profile directory</span></span> |

<span data-ttu-id="52d50-603">시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-603">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="52d50-604">이 항목의 구성 공급자는 코드에서 정렬하는 순서가 아니라 사전 순으로 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-604">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="52d50-605">앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-605">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="52d50-606">구성 공급자의 일반적인 순서는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-606">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="52d50-607">파일( *appsettings.json* , *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)</span><span class="sxs-lookup"><span data-stu-id="52d50-607">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="52d50-608">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="52d50-608">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="52d50-609">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)</span><span class="sxs-lookup"><span data-stu-id="52d50-609">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="52d50-610">환경 변수</span><span class="sxs-lookup"><span data-stu-id="52d50-610">Environment variables</span></span>
1. <span data-ttu-id="52d50-611">명령줄 인수</span><span class="sxs-lookup"><span data-stu-id="52d50-611">Command-line arguments</span></span>

<span data-ttu-id="52d50-612">일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-612">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="52d50-613">`CreateDefaultBuilder`로 새 호스트 작성기가 초기화될 때 이전 공급자 시퀀스가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-613">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52d50-614">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-614">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="52d50-615">UseConfiguration을 사용하여 호스트 작성기 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-615">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="52d50-616">호스트 작성기를 구성하려면 구성과 함께 호스트 작성기에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-616">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="52d50-617">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="52d50-617">ConfigureAppConfiguration</span></span>

<span data-ttu-id="52d50-618">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-618">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="52d50-619">명령줄 인수를 사용하여 이전 구성 재정의</span><span class="sxs-lookup"><span data-stu-id="52d50-619">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="52d50-620">명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-620">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="52d50-621">CreateDefaultBuilder에 의해 추가된 공급자 제거</span><span class="sxs-lookup"><span data-stu-id="52d50-621">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="52d50-622">`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-622">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="52d50-623">앱 시작 중 구성 사용</span><span class="sxs-lookup"><span data-stu-id="52d50-623">Consume configuration during app startup</span></span>

<span data-ttu-id="52d50-624">`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-624">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="52d50-625">자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-625">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="52d50-626">명령줄 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-626">Command-line Configuration Provider</span></span>

<span data-ttu-id="52d50-627"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-627">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="52d50-628">명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-628">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52d50-629">`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-629">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="52d50-630">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-630">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52d50-631">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-631">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="52d50-632">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성.</span><span class="sxs-lookup"><span data-stu-id="52d50-632">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="52d50-633">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="52d50-633">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52d50-634">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="52d50-634">Environment variables.</span></span>

<span data-ttu-id="52d50-635">`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-635">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="52d50-636">따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-636">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="52d50-637">`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-637">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="52d50-638">따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-638">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="52d50-639">ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-639">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52d50-640">추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-640">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="52d50-641">**예제**</span><span class="sxs-lookup"><span data-stu-id="52d50-641">**Example**</span></span>

<span data-ttu-id="52d50-642">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-642">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="52d50-643">프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-643">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="52d50-644">`dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).</span><span class="sxs-lookup"><span data-stu-id="52d50-644">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="52d50-645">앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-645">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="52d50-646">`dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-646">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="52d50-647">인수</span><span class="sxs-lookup"><span data-stu-id="52d50-647">Arguments</span></span>

<span data-ttu-id="52d50-648">값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-648">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="52d50-649">등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="52d50-649">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="52d50-650">키 접두사</span><span class="sxs-lookup"><span data-stu-id="52d50-650">Key prefix</span></span>               | <span data-ttu-id="52d50-651">예제</span><span class="sxs-lookup"><span data-stu-id="52d50-651">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="52d50-652">접두사 없음</span><span class="sxs-lookup"><span data-stu-id="52d50-652">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="52d50-653">대시 2개(`--`)</span><span class="sxs-lookup"><span data-stu-id="52d50-653">Two dashes (`--`)</span></span>        | <span data-ttu-id="52d50-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="52d50-654">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="52d50-655">슬래시(`/`)</span><span class="sxs-lookup"><span data-stu-id="52d50-655">Forward slash (`/`)</span></span>      | <span data-ttu-id="52d50-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="52d50-656">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="52d50-657">같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-657">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="52d50-658">명령 예:</span><span class="sxs-lookup"><span data-stu-id="52d50-658">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="52d50-659">스위치 매핑</span><span class="sxs-lookup"><span data-stu-id="52d50-659">Switch mappings</span></span>

<span data-ttu-id="52d50-660">스위치 매핑은 키 이름 교체 논리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-660">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="52d50-661"><xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-661">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="52d50-662">스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-662">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="52d50-663">사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-663">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="52d50-664">단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-664">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="52d50-665">스위치 매핑 사전 키 규칙:</span><span class="sxs-lookup"><span data-stu-id="52d50-665">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="52d50-666">스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-666">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="52d50-667">스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-667">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="52d50-668">스위치 매핑 사전을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-668">Create a switch mappings dictionary.</span></span> <span data-ttu-id="52d50-669">다음 예에서는 두 개의 스위치 매핑이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-669">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="52d50-670">호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-670">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="52d50-671">스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-671">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="52d50-672">`CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-672">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52d50-673">해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-673">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="52d50-674">생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-674">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="52d50-675">키</span><span class="sxs-lookup"><span data-stu-id="52d50-675">Key</span></span>       | <span data-ttu-id="52d50-676">값</span><span class="sxs-lookup"><span data-stu-id="52d50-676">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="52d50-677">앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-677">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="52d50-678">앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-678">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="52d50-679">Key</span><span class="sxs-lookup"><span data-stu-id="52d50-679">Key</span></span>               | <span data-ttu-id="52d50-680">값</span><span class="sxs-lookup"><span data-stu-id="52d50-680">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="52d50-681">환경 변수 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-681">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="52d50-682"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-682">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="52d50-683">환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-683">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="52d50-684">[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-684">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="52d50-685">자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-685">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="52d50-686">새 호스트 작성기가 [웹 호스트](xref:fundamentals/host/web-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `ASPNETCORE_`인 환경 변수를 로드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-686">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="52d50-687">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-687">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52d50-688">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-688">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="52d50-689">접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성</span><span class="sxs-lookup"><span data-stu-id="52d50-689">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="52d50-690">*appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성.</span><span class="sxs-lookup"><span data-stu-id="52d50-690">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="52d50-691">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="52d50-691">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52d50-692">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="52d50-692">Command-line arguments.</span></span>

<span data-ttu-id="52d50-693">사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-693">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="52d50-694">이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-694">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="52d50-695">추가 환경 변수에서 앱 구성을 제공하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-695">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="52d50-696">지정된 접두사를 포함하는 환경 변수에서 다른 공급자의 값을 재정의할 수 있도록 하려면 `AddEnvironmentVariables`를 마지막으로 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-696">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="52d50-697">**예제**</span><span class="sxs-lookup"><span data-stu-id="52d50-697">**Example**</span></span>

<span data-ttu-id="52d50-698">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-698">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="52d50-699">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-699">Run the sample app.</span></span> <span data-ttu-id="52d50-700">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-700">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="52d50-701">환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-701">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="52d50-702">이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-702">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="52d50-703">앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-703">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="52d50-704">샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-704">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="52d50-705">앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs* 에서 `FilteredConfiguration`을 다음으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-705">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="52d50-706">접두사</span><span class="sxs-lookup"><span data-stu-id="52d50-706">Prefixes</span></span>

<span data-ttu-id="52d50-707">`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-707">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="52d50-708">예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-708">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="52d50-709">구성 키-값 쌍이 생성되면 접두사는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-709">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="52d50-710">호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-710">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="52d50-711">환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-711">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52d50-712">**연결 문자열 접두사**</span><span class="sxs-lookup"><span data-stu-id="52d50-712">**Connection string prefixes**</span></span>

<span data-ttu-id="52d50-713">구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-713">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="52d50-714">즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-714">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="52d50-715">연결 문자열 접두사</span><span class="sxs-lookup"><span data-stu-id="52d50-715">Connection string prefix</span></span> | <span data-ttu-id="52d50-716">공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-716">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="52d50-717">사용자 지정 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-717">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="52d50-718">MySQL</span><span class="sxs-lookup"><span data-stu-id="52d50-718">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="52d50-719">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="52d50-719">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="52d50-720">SQL Server</span><span class="sxs-lookup"><span data-stu-id="52d50-720">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="52d50-721">표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-721">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="52d50-722">환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-722">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="52d50-723">데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-723">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="52d50-724">환경 변수 키</span><span class="sxs-lookup"><span data-stu-id="52d50-724">Environment variable key</span></span> | <span data-ttu-id="52d50-725">변환된 구성 키</span><span class="sxs-lookup"><span data-stu-id="52d50-725">Converted configuration key</span></span> | <span data-ttu-id="52d50-726">공급자 구성 항목</span><span class="sxs-lookup"><span data-stu-id="52d50-726">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-727">구성 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-727">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-728">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="52d50-728">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52d50-729">값: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="52d50-729">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-730">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="52d50-730">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52d50-731">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="52d50-731">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="52d50-732">키: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="52d50-732">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="52d50-733">값: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="52d50-733">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="52d50-734">**예제**</span><span class="sxs-lookup"><span data-stu-id="52d50-734">**Example**</span></span>

<span data-ttu-id="52d50-735">서버에 사용자 지정 연결 문자열 환경 변수가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-735">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="52d50-736">이름: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="52d50-736">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="52d50-737">값: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="52d50-737">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="52d50-738">`IConfiguration`이 삽입되고 `_config`라는 필드에 할당된 경우 값을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-738">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="52d50-739">파일 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-739">File Configuration Provider</span></span>

<span data-ttu-id="52d50-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-740"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="52d50-741">다음 구성 공급자는 특정 파일 형식 전용입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-741">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="52d50-742">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-742">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="52d50-743">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-743">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="52d50-744">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-744">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="52d50-745">INI 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-745">INI Configuration Provider</span></span>

<span data-ttu-id="52d50-746"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-746">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="52d50-747">INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-747">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52d50-748">콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-748">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="52d50-749">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-749">Overloads permit specifying:</span></span>

* <span data-ttu-id="52d50-750">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-750">Whether the file is optional.</span></span>
* <span data-ttu-id="52d50-751">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-751">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="52d50-752">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="52d50-752">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="52d50-753">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-753">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="52d50-754">INI 구성 파일의 일반적인 예:</span><span class="sxs-lookup"><span data-stu-id="52d50-754">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="52d50-755">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-755">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52d50-756">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-756">section0:key0</span></span>
* <span data-ttu-id="52d50-757">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-757">section0:key1</span></span>
* <span data-ttu-id="52d50-758">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="52d50-758">section1:subsection:key</span></span>
* <span data-ttu-id="52d50-759">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="52d50-759">section2:subsection0:key</span></span>
* <span data-ttu-id="52d50-760">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="52d50-760">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="52d50-761">JSON 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-761">JSON Configuration Provider</span></span>

<span data-ttu-id="52d50-762"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-762">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="52d50-763">JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-763">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52d50-764">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-764">Overloads permit specifying:</span></span>

* <span data-ttu-id="52d50-765">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-765">Whether the file is optional.</span></span>
* <span data-ttu-id="52d50-766">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-766">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="52d50-767">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="52d50-767">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="52d50-768">`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-768">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="52d50-769">이 메서드는 호출되면 다음에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-769">The method is called to load configuration from:</span></span>

* <span data-ttu-id="52d50-770">*appsettings.json* : 이 파일을 먼저 읽었습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-770">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="52d50-771">파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-771">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="52d50-772">*appsettings.{Environment}.json*: 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-772">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="52d50-773">자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-773">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="52d50-774">`CreateDefaultBuilder`는 다음 항목도 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-774">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="52d50-775">환경 변수.</span><span class="sxs-lookup"><span data-stu-id="52d50-775">Environment variables.</span></span>
* <span data-ttu-id="52d50-776">[사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)</span><span class="sxs-lookup"><span data-stu-id="52d50-776">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="52d50-777">명령줄 인수.</span><span class="sxs-lookup"><span data-stu-id="52d50-777">Command-line arguments.</span></span>

<span data-ttu-id="52d50-778">JSON 구성 공급자를 먼저 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-778">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="52d50-779">따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-779">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="52d50-780">*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-780">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="52d50-781">**예제**</span><span class="sxs-lookup"><span data-stu-id="52d50-781">**Example**</span></span>

<span data-ttu-id="52d50-782">샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-782">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="52d50-783">`AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json* 에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-783">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="52d50-784">`AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json* 에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-784">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="52d50-785">샘플 앱의 *appsettings.Development.json* 의 경우 다음 파일이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-785">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="52d50-786">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-786">Run the sample app.</span></span> <span data-ttu-id="52d50-787">브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-787">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="52d50-788">출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-788">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="52d50-789">개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-789">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="52d50-790">프로덕션 환경에서 샘플 앱을 다시 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-790">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="52d50-791">*Properties/launchSettings.json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-791">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="52d50-792">`ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-792">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="52d50-793">파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-793">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="52d50-794">*appsettings.Development.json* 의 설정에서 더 이상 *appsettings.json* 의 설정을 재정의하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-794">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="52d50-795">`Logging:LogLevel:Default` 키의 로그 수준은 `Warning`입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-795">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="52d50-796">XML 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-796">XML Configuration Provider</span></span>

<span data-ttu-id="52d50-797"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-797">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="52d50-798">XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-798">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52d50-799">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-799">Overloads permit specifying:</span></span>

* <span data-ttu-id="52d50-800">파일이 선택 사항인지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-800">Whether the file is optional.</span></span>
* <span data-ttu-id="52d50-801">파일이 변경되면 구성을 다시 로드하는지 여부</span><span class="sxs-lookup"><span data-stu-id="52d50-801">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="52d50-802">파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider></span><span class="sxs-lookup"><span data-stu-id="52d50-802">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="52d50-803">구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-803">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="52d50-804">파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-804">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="52d50-805">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-805">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="52d50-806">XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-806">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="52d50-807">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-807">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52d50-808">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-808">section0:key0</span></span>
* <span data-ttu-id="52d50-809">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-809">section0:key1</span></span>
* <span data-ttu-id="52d50-810">section1:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-810">section1:key0</span></span>
* <span data-ttu-id="52d50-811">section1:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-811">section1:key1</span></span>

<span data-ttu-id="52d50-812">같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-812">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="52d50-813">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-813">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52d50-814">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-814">section:section0:key:key0</span></span>
* <span data-ttu-id="52d50-815">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-815">section:section0:key:key1</span></span>
* <span data-ttu-id="52d50-816">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-816">section:section1:key:key0</span></span>
* <span data-ttu-id="52d50-817">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-817">section:section1:key:key1</span></span>

<span data-ttu-id="52d50-818">특성을 사용하여 값을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-818">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="52d50-819">이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-819">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="52d50-820">key:attribute</span><span class="sxs-lookup"><span data-stu-id="52d50-820">key:attribute</span></span>
* <span data-ttu-id="52d50-821">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="52d50-821">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="52d50-822">파일별 키 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-822">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="52d50-823"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-823">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="52d50-824">키는 파일 이름이고,</span><span class="sxs-lookup"><span data-stu-id="52d50-824">The key is the file name.</span></span> <span data-ttu-id="52d50-825">값은 파일의 콘텐츠를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-825">The value contains the file's contents.</span></span> <span data-ttu-id="52d50-826">파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-826">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="52d50-827">파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-827">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="52d50-828">파일의 `directoryPath`는 절대 경로여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-828">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="52d50-829">오버로드에서는 다음을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-829">Overloads permit specifying:</span></span>

* <span data-ttu-id="52d50-830">소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자</span><span class="sxs-lookup"><span data-stu-id="52d50-830">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="52d50-831">디렉터리가 선택 사항인지 여부와 디렉터리의 경로</span><span class="sxs-lookup"><span data-stu-id="52d50-831">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="52d50-832">두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-832">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="52d50-833">예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-833">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="52d50-834">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="52d50-835">메모리 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-835">Memory Configuration Provider</span></span>

<span data-ttu-id="52d50-836"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-836">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="52d50-837">메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-837">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="52d50-838">`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-838">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="52d50-839">호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-839">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="52d50-840">다음 예제에서는 구성 사전이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-840">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="52d50-841">사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-841">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="52d50-842">GetValue</span><span class="sxs-lookup"><span data-stu-id="52d50-842">GetValue</span></span>

<span data-ttu-id="52d50-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 단일 값을 추출하고 해당 값을 지정된 컬렉션이 아닌 형식으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-843">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="52d50-844">오버로드는 기본값을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-844">An overload accepts a default value.</span></span>

<span data-ttu-id="52d50-845">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="52d50-845">The following example:</span></span>

* <span data-ttu-id="52d50-846">`NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-846">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="52d50-847">구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-847">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="52d50-848">값을 `int` 형식으로 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-848">Types the value as an `int`.</span></span>
* <span data-ttu-id="52d50-849">페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-849">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="52d50-850">GetSection, GetChildren 및 Exists</span><span class="sxs-lookup"><span data-stu-id="52d50-850">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="52d50-851">이어지는 예제에서는 다음 JSON 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-851">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="52d50-852">네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-852">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="52d50-853">파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-853">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="52d50-854">section0:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-854">section0:key0</span></span>
* <span data-ttu-id="52d50-855">section0:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-855">section0:key1</span></span>
* <span data-ttu-id="52d50-856">section1:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-856">section1:key0</span></span>
* <span data-ttu-id="52d50-857">section1:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-857">section1:key1</span></span>
* <span data-ttu-id="52d50-858">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-858">section2:subsection0:key0</span></span>
* <span data-ttu-id="52d50-859">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-859">section2:subsection0:key1</span></span>
* <span data-ttu-id="52d50-860">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="52d50-860">section2:subsection1:key0</span></span>
* <span data-ttu-id="52d50-861">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="52d50-861">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="52d50-862">GetSection</span><span class="sxs-lookup"><span data-stu-id="52d50-862">GetSection</span></span>

<span data-ttu-id="52d50-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-863">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="52d50-864">`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-864">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="52d50-865">`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-865">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="52d50-866">마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-866">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="52d50-867">`GetSection`은 `null`을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-867">`GetSection` never returns `null`.</span></span> <span data-ttu-id="52d50-868">일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-868">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="52d50-869">`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-869">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="52d50-870">섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-870">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="52d50-871">GetChildren</span><span class="sxs-lookup"><span data-stu-id="52d50-871">GetChildren</span></span>

<span data-ttu-id="52d50-872">`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-872">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="52d50-873">있음</span><span class="sxs-lookup"><span data-stu-id="52d50-873">Exists</span></span>

<span data-ttu-id="52d50-874">[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-874">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="52d50-875">예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-875">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="52d50-876">개체 그래프에 바인딩</span><span class="sxs-lookup"><span data-stu-id="52d50-876">Bind to an object graph</span></span>

<span data-ttu-id="52d50-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-877"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="52d50-878">단순 개체 바인딩과 마찬가지로 공용 읽기/쓰기 속성만 바인딩되었습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-878">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="52d50-879">다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-879">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="52d50-880">샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-880">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="52d50-881">`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-881">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="52d50-882">바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-882">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="52d50-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-883">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="52d50-884">`Get<T>`가 `Bind`보다 사용하기 더 간편합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-884">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="52d50-885">다음 코드는 위의 예제에 `Get<T>`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-885">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="52d50-886">클래스에 배열 바인딩</span><span class="sxs-lookup"><span data-stu-id="52d50-886">Bind an array to a class</span></span>

<span data-ttu-id="52d50-887">다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-887">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="52d50-888"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-888">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="52d50-889">숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-889">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="52d50-890">바인딩은 규칙에 따라 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-890">Binding is provided by convention.</span></span> <span data-ttu-id="52d50-891">배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-891">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="52d50-892">**메모리 내 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="52d50-892">**In-memory array processing**</span></span>

<span data-ttu-id="52d50-893">다음 표에 표시된 구성 키 및 값을 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-893">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="52d50-894">Key</span><span class="sxs-lookup"><span data-stu-id="52d50-894">Key</span></span>             | <span data-ttu-id="52d50-895">값</span><span class="sxs-lookup"><span data-stu-id="52d50-895">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="52d50-896">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="52d50-896">array:entries:0</span></span> | <span data-ttu-id="52d50-897">value0</span><span class="sxs-lookup"><span data-stu-id="52d50-897">value0</span></span> |
| <span data-ttu-id="52d50-898">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="52d50-898">array:entries:1</span></span> | <span data-ttu-id="52d50-899">value1</span><span class="sxs-lookup"><span data-stu-id="52d50-899">value1</span></span> |
| <span data-ttu-id="52d50-900">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="52d50-900">array:entries:2</span></span> | <span data-ttu-id="52d50-901">value2</span><span class="sxs-lookup"><span data-stu-id="52d50-901">value2</span></span> |
| <span data-ttu-id="52d50-902">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="52d50-902">array:entries:4</span></span> | <span data-ttu-id="52d50-903">value4</span><span class="sxs-lookup"><span data-stu-id="52d50-903">value4</span></span> |
| <span data-ttu-id="52d50-904">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="52d50-904">array:entries:5</span></span> | <span data-ttu-id="52d50-905">value5</span><span class="sxs-lookup"><span data-stu-id="52d50-905">value5</span></span> |

<span data-ttu-id="52d50-906">메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-906">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="52d50-907">배열은 인덱스 &num;3에 대한 값을 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-907">The array skips a value for index &num;3.</span></span> <span data-ttu-id="52d50-908">구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-908">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="52d50-909">샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-909">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="52d50-910">구성 데이터는 개체에 바인딩됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-910">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="52d50-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문을 사용할 수도 있으며, 이 경우 코드가 더 간결해집니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-911">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="52d50-912">바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-912">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="52d50-913">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="52d50-913">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="52d50-914">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="52d50-914">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="52d50-915">0</span><span class="sxs-lookup"><span data-stu-id="52d50-915">0</span></span>                            | <span data-ttu-id="52d50-916">value0</span><span class="sxs-lookup"><span data-stu-id="52d50-916">value0</span></span>                       |
| <span data-ttu-id="52d50-917">1</span><span class="sxs-lookup"><span data-stu-id="52d50-917">1</span></span>                            | <span data-ttu-id="52d50-918">value1</span><span class="sxs-lookup"><span data-stu-id="52d50-918">value1</span></span>                       |
| <span data-ttu-id="52d50-919">2</span><span class="sxs-lookup"><span data-stu-id="52d50-919">2</span></span>                            | <span data-ttu-id="52d50-920">value2</span><span class="sxs-lookup"><span data-stu-id="52d50-920">value2</span></span>                       |
| <span data-ttu-id="52d50-921">3</span><span class="sxs-lookup"><span data-stu-id="52d50-921">3</span></span>                            | <span data-ttu-id="52d50-922">value4</span><span class="sxs-lookup"><span data-stu-id="52d50-922">value4</span></span>                       |
| <span data-ttu-id="52d50-923">4</span><span class="sxs-lookup"><span data-stu-id="52d50-923">4</span></span>                            | <span data-ttu-id="52d50-924">value5</span><span class="sxs-lookup"><span data-stu-id="52d50-924">value5</span></span>                       |

<span data-ttu-id="52d50-925">바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-925">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="52d50-926">배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-926">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="52d50-927">구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-927">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="52d50-928">인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-928">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="52d50-929">샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-929">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="52d50-930">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="52d50-930">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="52d50-931">`ConfigureAppConfiguration`의 경우</span><span class="sxs-lookup"><span data-stu-id="52d50-931">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="52d50-932">표에 표시된 키-값 쌍이 구성으로 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-932">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="52d50-933">키</span><span class="sxs-lookup"><span data-stu-id="52d50-933">Key</span></span>             | <span data-ttu-id="52d50-934">값</span><span class="sxs-lookup"><span data-stu-id="52d50-934">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="52d50-935">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="52d50-935">array:entries:3</span></span> | <span data-ttu-id="52d50-936">value3</span><span class="sxs-lookup"><span data-stu-id="52d50-936">value3</span></span> |

<span data-ttu-id="52d50-937">JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-937">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="52d50-938">`ArrayExample.Entries` 인덱스</span><span class="sxs-lookup"><span data-stu-id="52d50-938">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="52d50-939">`ArrayExample.Entries` 값</span><span class="sxs-lookup"><span data-stu-id="52d50-939">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="52d50-940">0</span><span class="sxs-lookup"><span data-stu-id="52d50-940">0</span></span>                            | <span data-ttu-id="52d50-941">value0</span><span class="sxs-lookup"><span data-stu-id="52d50-941">value0</span></span>                       |
| <span data-ttu-id="52d50-942">1</span><span class="sxs-lookup"><span data-stu-id="52d50-942">1</span></span>                            | <span data-ttu-id="52d50-943">value1</span><span class="sxs-lookup"><span data-stu-id="52d50-943">value1</span></span>                       |
| <span data-ttu-id="52d50-944">2</span><span class="sxs-lookup"><span data-stu-id="52d50-944">2</span></span>                            | <span data-ttu-id="52d50-945">value2</span><span class="sxs-lookup"><span data-stu-id="52d50-945">value2</span></span>                       |
| <span data-ttu-id="52d50-946">3</span><span class="sxs-lookup"><span data-stu-id="52d50-946">3</span></span>                            | <span data-ttu-id="52d50-947">value3</span><span class="sxs-lookup"><span data-stu-id="52d50-947">value3</span></span>                       |
| <span data-ttu-id="52d50-948">4</span><span class="sxs-lookup"><span data-stu-id="52d50-948">4</span></span>                            | <span data-ttu-id="52d50-949">value4</span><span class="sxs-lookup"><span data-stu-id="52d50-949">value4</span></span>                       |
| <span data-ttu-id="52d50-950">5</span><span class="sxs-lookup"><span data-stu-id="52d50-950">5</span></span>                            | <span data-ttu-id="52d50-951">value5</span><span class="sxs-lookup"><span data-stu-id="52d50-951">value5</span></span>                       |

<span data-ttu-id="52d50-952">**JSON 배열 처리**</span><span class="sxs-lookup"><span data-stu-id="52d50-952">**JSON array processing**</span></span>

<span data-ttu-id="52d50-953">JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-953">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="52d50-954">다음 구성 파일에서 `subsection`은 배열입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-954">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="52d50-955">JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-955">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="52d50-956">Key</span><span class="sxs-lookup"><span data-stu-id="52d50-956">Key</span></span>                     | <span data-ttu-id="52d50-957">값</span><span class="sxs-lookup"><span data-stu-id="52d50-957">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="52d50-958">json_array:key</span><span class="sxs-lookup"><span data-stu-id="52d50-958">json_array:key</span></span>          | <span data-ttu-id="52d50-959">valueA</span><span class="sxs-lookup"><span data-stu-id="52d50-959">valueA</span></span> |
| <span data-ttu-id="52d50-960">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="52d50-960">json_array:subsection:0</span></span> | <span data-ttu-id="52d50-961">valueB</span><span class="sxs-lookup"><span data-stu-id="52d50-961">valueB</span></span> |
| <span data-ttu-id="52d50-962">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="52d50-962">json_array:subsection:1</span></span> | <span data-ttu-id="52d50-963">valueC</span><span class="sxs-lookup"><span data-stu-id="52d50-963">valueC</span></span> |
| <span data-ttu-id="52d50-964">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="52d50-964">json_array:subsection:2</span></span> | <span data-ttu-id="52d50-965">valueD</span><span class="sxs-lookup"><span data-stu-id="52d50-965">valueD</span></span> |

<span data-ttu-id="52d50-966">샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-966">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="52d50-967">바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-967">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="52d50-968">하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-968">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="52d50-969">`JsonArrayExample.Subsection` 인덱스</span><span class="sxs-lookup"><span data-stu-id="52d50-969">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="52d50-970">`JsonArrayExample.Subsection` 값</span><span class="sxs-lookup"><span data-stu-id="52d50-970">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="52d50-971">0</span><span class="sxs-lookup"><span data-stu-id="52d50-971">0</span></span>                                   | <span data-ttu-id="52d50-972">valueB</span><span class="sxs-lookup"><span data-stu-id="52d50-972">valueB</span></span>                              |
| <span data-ttu-id="52d50-973">1</span><span class="sxs-lookup"><span data-stu-id="52d50-973">1</span></span>                                   | <span data-ttu-id="52d50-974">valueC</span><span class="sxs-lookup"><span data-stu-id="52d50-974">valueC</span></span>                              |
| <span data-ttu-id="52d50-975">2</span><span class="sxs-lookup"><span data-stu-id="52d50-975">2</span></span>                                   | <span data-ttu-id="52d50-976">valueD</span><span class="sxs-lookup"><span data-stu-id="52d50-976">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="52d50-977">사용자 지정 구성 공급자</span><span class="sxs-lookup"><span data-stu-id="52d50-977">Custom configuration provider</span></span>

<span data-ttu-id="52d50-978">샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-978">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="52d50-979">이 공급자의 특징은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-979">The provider has the following characteristics:</span></span>

* <span data-ttu-id="52d50-980">데모용으로 EF 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-980">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="52d50-981">연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-981">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="52d50-982">이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-982">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="52d50-983">이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-983">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="52d50-984">변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-984">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="52d50-985">데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-985">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="52d50-986">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-986">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="52d50-987">구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-987">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="52d50-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-988">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="52d50-989"><xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-989">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="52d50-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-990">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="52d50-991"><xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-991">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="52d50-992">구성 공급자는 비어 있는 데이터베이스를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-992">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="52d50-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-993">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="52d50-994">`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-994">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="52d50-995">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="52d50-995">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="52d50-996">다음 코드는 *Program.cs* 에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-996">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="52d50-997">시작하는 동안 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="52d50-997">Access configuration during startup</span></span>

<span data-ttu-id="52d50-998">`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-998">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="52d50-999">`Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-999">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="52d50-1000">시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-1000">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="52d50-1001">Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스</span><span class="sxs-lookup"><span data-stu-id="52d50-1001">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="52d50-1002">Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-1002">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="52d50-1003">Razor Pages 페이지에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-1003">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="52d50-1004">MVC 뷰에서:</span><span class="sxs-lookup"><span data-stu-id="52d50-1004">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="52d50-1005">외부 어셈블리의 구성 추가</span><span class="sxs-lookup"><span data-stu-id="52d50-1005">Add configuration from an external assembly</span></span>

<span data-ttu-id="52d50-1006"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52d50-1006">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="52d50-1007">자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52d50-1007">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52d50-1008">추가 자료</span><span class="sxs-lookup"><span data-stu-id="52d50-1008">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
