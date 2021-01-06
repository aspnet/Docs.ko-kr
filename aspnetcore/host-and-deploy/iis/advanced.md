---
title: 고급 구성
author: rick-anderson
description: ASP.NET Core 모듈 및 IIS(인터넷 정보 서비스)를 사용하는 고급 구성입니다.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058618"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="ab678-103">ASP.NET Core 모듈 및 IIS의 고급 구성</span><span class="sxs-lookup"><span data-stu-id="ab678-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="ab678-104">이 문서에서는 ASP.NET Core 모듈 및 IIS의 고급 구성 옵션 및 시나리오를 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="ab678-105">스택 크기 수정</span><span class="sxs-lookup"><span data-stu-id="ab678-105">Modify the stack size</span></span>

<span data-ttu-id="ab678-106">*In-process 호스팅 모델을 사용하는 경우에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="ab678-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="ab678-107">`web.config` 파일에서 `stackSize` 설정을 사용하여 관리형 스택 크기(바이트)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="ab678-108">기본 크기는 1,048,576 bytes(1MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="ab678-109">다음 예제에서는 스택 크기를 2MB(2,097,152바이트)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="ab678-110">프록시 구성은 HTTP 프로토콜 및 페어링 토큰을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="ab678-111">*호스팅에만 적용됩니다.*</span><span class="sxs-lookup"><span data-stu-id="ab678-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="ab678-112">ASP.NET Core 모듈과 Kestrel 사이에 만들어진 프록시는 HTTP 프로토콜을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="ab678-113">서버에서 분리된 위치에서 모듈과 Kestrel 간 트래픽 도청의 위험은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="ab678-114">페어링 토큰은 Kestrel에서 받은 요청이 IIS에서 프록시되었으며 다른 원본에서 오지 않았다는 것을 보장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="ab678-115">페어링 토큰은 모듈에 의해 생성되며 환경 변수(`ASPNETCORE_TOKEN`)로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="ab678-116">페어링 토큰은 프록시된 모든 요청에서 헤더(`MS-ASPNETCORE-TOKEN`)로도 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="ab678-117">IIS 미들웨어는 수신하는 각 요청을 검사하여 페어링 토큰 헤더 값이 환경 변수 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="ab678-118">토큰 값이 일치하지 않는 경우 요청이 기록되고 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="ab678-119">페어링 토큰 환경 변수와 모듈과 Kestrel 간의 트래픽은 서버에서 분리된 위치에서 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="ab678-120">페어링 토큰 값을 알지 못하면 공격자는 IIS 미들웨어에서 검사를 무시하는 요청을 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="ab678-121">IIS 공유 구성이 포함된 ASP.NET Core 모듈</span><span class="sxs-lookup"><span data-stu-id="ab678-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="ab678-122">ASP.NET Core 모듈 설치 관리자는 `TrustedInstaller` 계정의 권한으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="ab678-123">로컬 시스템 계정에는 IIS 공유 구성에서 사용하는 공유 경로에 대한 수정 권한이 없으므로 공유의 `applicationHost.config`에서 모듈 설정을 구성하려고 하면 설치 관리자에서 액세스 거부 오류가 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="ab678-124">IIS 설치와 동일한 머신에서 IIS 공유 구성을 사용하는 경우 `OPT_NO_SHARED_CONFIG_CHECK` 매개 변수를 `1`로 설정한 상태에서 ASP.NET Core Hosting Bundle 설치 관리자를 실행합니다</span><span class="sxs-lookup"><span data-stu-id="ab678-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="ab678-125">공유 구성에 대한 경로가 IIS 설치와 동일한 머신에 있지 않으면 다음 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="ab678-126">IIS 공유 구성을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="ab678-127">설치 관리자를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-127">Run the installer.</span></span>
1. <span data-ttu-id="ab678-128">업데이트된 `applicationHost.config` 파일을 파일 공유로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="ab678-129">IIS 공유 구성을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="ab678-130">데이터 보호</span><span class="sxs-lookup"><span data-stu-id="ab678-130">Data protection</span></span>

<span data-ttu-id="ab678-131">[ASP.NET Core 데이터 보호 스택](xref:security/data-protection/introduction)은 인증에 사용되는 미들웨어를 포함하여 여러 ASP.NET Core [미들웨어](xref:fundamentals/middleware/index)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="ab678-132">사용자 코드에서 데이터 보호 API가 호출되지 않더라도 배포 스크립트 또는 사용자 코드를 통해 영구적 암호화 [키 저장소](xref:security/data-protection/implementation/key-management)를 만들도록 데이터 보호를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="ab678-133">데이터 보호를 구성하지 않으면 키는 메모리에 보관되고 앱이 다시 시작되면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="ab678-134">데이터 보호 키 링이 메모리에 저장된 경우 앱을 다시 시작하면 다음과 같이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="ab678-135">모든 cookie 기반 인증 토큰이 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="ab678-136">사용자는 다음 요청에서 다시 로그인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="ab678-137">키 링으로 보호된 데이터의 암호를 더 이상 해독할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="ab678-138">여기에는 [CSRF 토큰](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 및 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)가 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="ab678-139">IIS에서 키 링을 저장하도록 데이터 보호를 구성하려면 다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="ab678-140">**데이터 보호 레지스트리 키 만들기**</span><span class="sxs-lookup"><span data-stu-id="ab678-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="ab678-141">ASP.NET Core 앱에서 사용되는 데이터 보호 키는 앱 외부의 레지스트리에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="ab678-142">지정된 앱의 키를 저장하려면 앱 풀에 대한 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="ab678-143">WebFarm이 아닌 독립 실행형 IIS 설치의 경우 [Data Protection Provision-AutoGenKeys.ps1 PowerShell 스크립트](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)를 ASP.NET Core 앱과 함께 사용되는 각 응용 프로그램 풀에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="ab678-144">이 스크립트는 앱의 앱 풀 작업자 프로세스 계정만 액세스할 수 있는 HKLM 레지스트리에 레지스트리 키를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="ab678-145">미사용 키는 컴퓨터 전체 키가 있는 DPAPI를 사용하여 암호화됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="ab678-146">웹 팜 시나리오에서는 UNC 경로를 사용하여 데이터 보호 키 링을 저장하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="ab678-147">기본적으로 키는 암호화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="ab678-148">네트워크 공유에 대한 파일 권한은 앱 실행에 사용되는 Windows 계정으로 제한되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="ab678-149">X509 인증서를 사용하여 미사용 키를 보호할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="ab678-150">사용자가 인증서를 업로드할 수 있는 메커니즘을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="ab678-151">즉, 사용자의 신뢰할 수 있는 인증서 저장소에 인증서를 배치하고, 사용자의 앱이 실행되는 모든 머신에서 이 인증서를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="ab678-152">자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="ab678-153">**사용자 프로필을 로드하도록 IIS 애플리케이션 풀 구성**</span><span class="sxs-lookup"><span data-stu-id="ab678-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="ab678-154">이 설정은 앱 풀에 대한 **고급 설정** 아래의 **프로세스 모델** 섹션에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="ab678-155">**사용자 프로필** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="ab678-156">`True`로 설정하면 키가 사용자 프로필 디렉터리에 저장되고, 사용자 계정에 관련된 키가 있는 DPAPI를 사용하여 보호됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="ab678-157">키는 `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` 폴더에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="ab678-158">앱 풀의 [`setProfileEnvironment` 특성](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)도 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="ab678-159">`setProfileEnvironment` 의 기본값은 `true`입니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="ab678-160">Windows OS와 같은 일부 시나리오에서는 `setProfileEnvironment`가 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="ab678-161">키가 예상대로 사용자 프로필 디렉터리에 저장되지 않는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="ab678-162">`%windir%/system32/inetsrv/config` 폴더로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="ab678-163">`applicationHost.config` 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="ab678-164">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="ab678-165">`setProfileEnvironment` 특성이 존재하지 않는지 확인합니다. 존재하지 않을 경우 기본값이 `true`로 설정됩니다. 또는 특성 값을 명시적으로 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="ab678-166">**파일 시스템을 키 링 저장소로 사용**</span><span class="sxs-lookup"><span data-stu-id="ab678-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="ab678-167">[파일 시스템을 키 링 저장소로 사용](xref:security/data-protection/configuration/overview)하도록 앱 코드를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="ab678-168">X509 인증서를 사용하여 키 링을 보호하고 신뢰할 수 있는 인증서인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="ab678-169">자체 서명된 인증서인 경우 신뢰할 수 있는 루트 저장소에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="ab678-170">웹 팜에서 IIS를 사용하는 경우 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="ab678-171">모든 컴퓨터에서 액세스할 수 있는 파일 공유를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="ab678-172">각 시스템에 X509 인증서를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="ab678-173">[코드에 데이터 보호](xref:security/data-protection/configuration/overview)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="ab678-174">**데이터 보호에 대한 머신 수준 정책 설정**</span><span class="sxs-lookup"><span data-stu-id="ab678-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="ab678-175">데이터 보호 시스템은 데이터 보호 API를 사용하는 모든 앱에 대한 기본 [머신 수준 정책](xref:security/data-protection/configuration/machine-wide-policy) 설정을 제한적으로 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="ab678-176">자세한 내용은 <xref:security/data-protection/introduction>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="ab678-177">IIS 구성</span><span class="sxs-lookup"><span data-stu-id="ab678-177">IIS configuration</span></span>

<span data-ttu-id="ab678-178">**Windows Server 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="ab678-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="ab678-179">**웹 서버(IIS)** 서버 역할을 사용하도록 설정하고 역할 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="ab678-180">**관리** 메뉴 또는 **서버 관리자** 의 링크를 통해 **역할 및 기능 추가** 마법사를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="ab678-181">**서버 역할** 단계에서 **웹 서버(IIS)** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![서버 역할 선택 단계에서 선택된 웹 서버 IIS 역할](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="ab678-183">**기능** 단계 후에는 웹 서버(IIS)에 대한 **역할 서비스** 단계가 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="ab678-184">원하는 IIS 역할 서비스를 선택하거나 제공된 기본 역할 서비스를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![역할 서비스 선택 단계에서 선택된 기본 역할 서비스](index/_static/role-services-ws2016.png)

   <span data-ttu-id="ab678-186">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="ab678-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ab678-187">Windows 인증을 사용하도록 설정하려면 **웹 서버** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="ab678-188">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ab678-189">자세한 내용은 [Windows 인증 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ab678-190">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="ab678-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ab678-191">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ab678-192">WebSocket을 사용하도록 설정하려면 **웹 서버** > **애플리케이션 개발** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="ab678-193">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ab678-194">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ab678-195">**확인** 단계를 진행하여 웹 서버 역할 및 서비스를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="ab678-196">**웹 서버(IIS)** 역할을 설치한 후에 서버/IIS를 다시 시작할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="ab678-197">**Windows 데스크톱 운영 체제**</span><span class="sxs-lookup"><span data-stu-id="ab678-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="ab678-198">**IIS 관리 콘솔** 및 **World Wide Web 서비스** 를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ab678-199">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="ab678-200">**인터넷 정보 서비스** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="ab678-201">**웹 관리 도구** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="ab678-202">**IIS 관리 콘솔** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="ab678-203">**World Wide Web 서비스** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="ab678-204">**World Wide Web 서비스** 의 기본 기능을 그대로 사용하거나 IIS 기능을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="ab678-205">**Windows 인증(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="ab678-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="ab678-206">Windows 인증을 사용하도록 설정하려면 **World Wide Web 서비스** > **보안** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="ab678-207">**Windows 인증** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="ab678-208">자세한 내용은 [Windows 인증 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 및 [Windows 인증 구성](xref:security/authentication/windowsauth)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="ab678-209">**WebSockets(선택 사항)**</span><span class="sxs-lookup"><span data-stu-id="ab678-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="ab678-210">WebSockets는 ASP.NET Core 1.1 이상에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="ab678-211">WebSocket을 사용하도록 설정하려면 **World Wide Web 서비스** > **애플리케이션 개발 기능** 노드를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="ab678-212">**WebSocket 프로토콜** 기능을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="ab678-213">자세한 내용은 [WebSocket](xref:fundamentals/websockets)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="ab678-214">IIS 설치 시 다시 시작해야 하는 경우 시스템을 다시 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-214">If the IIS installation requires a restart, restart the system.</span></span>

![Windows 기능에서 선택된 IIS 관리 콘솔 및 World Wide Web 서비스](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="ab678-216">가상 디렉터리</span><span class="sxs-lookup"><span data-stu-id="ab678-216">Virtual Directories</span></span>

<span data-ttu-id="ab678-217">[IIS 가상 디렉터리](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)는 ASP.NET Core 앱에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="ab678-218">앱은 [하위 애플리케이션](#sub-applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="ab678-219">하위 애플리케이션</span><span class="sxs-lookup"><span data-stu-id="ab678-219">Sub-applications</span></span>

<span data-ttu-id="ab678-220">ASP.NET Core 앱은 [IIS 하위 애플리케이션(하위 앱)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)으로 호스팅될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="ab678-221">하위 앱의 경로는 루트 앱 URL의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="ab678-222">하위 앱 내의 정적 자산 링크는 물결표-슬래시(`~/`) 표기법을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="ab678-223">물결표-슬래시 표기법은 [태그 도우미](xref:mvc/views/tag-helpers/intro)를 트리거하여 하위 앱의 PathBase를 렌더링된 상대 링크 앞에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="ab678-224">`/subapp_path`에서 하위 앱의 경우, `src="~/image.png"`와 연결된 이미지가 `src="/subapp_path/image.png"`으로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="ab678-225">루트 앱의 정적 파일 미들웨어는 정적 파일 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="ab678-226">요청은 하위 앱의 정적 파일 미들웨어에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="ab678-227">정적 자산의 `src` 특성이 절대 경로(예: `src="/image.png"`)로 설정된 경우 링크는 하위 앱의 PathBase 없이 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="ab678-228">루트 앱의 정적 파일 미들웨어는 루트 앱의 [웹 루트](xref:fundamentals/index#web-root)에서 자산을 제공하려고 시도하며, 그 결과 루트 앱에서 정적 자산을 사용할 수 있지 않으면 ‘404 - 찾을 수 없음’이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="ab678-229">ASP.NET Core 앱을 다른 ASP.NET Core 앱에서 하위 앱으로 호스팅하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="ab678-230">하위 앱에 대한 앱 풀을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="ab678-231">데스크톱 CLR(.NET CLR)이 아닌 .NET Core용 CoreCLR(Core 공용 언어 런타임)이 부팅되어 작업자 프로세스의 앱을 호스트하기 때문에 **.NET CLR 버전** 을 **관리 코드 없음** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="ab678-232">루트 사이트 아래의 폴더에 하위 앱을 사용하여 IIS 관리자에 루트 사이트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="ab678-233">IIS 관리자에서 하위 앱 폴더를 마우스 오른쪽 단추로 클릭하고 **Convert to Application**(애플리케이션으로 변환)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="ab678-234">**Add Application**(애플리케이션 추가) 대화 상자에서 **애플리케이션 풀** 에 대한 **선택** 단추를 사용하여 하위 앱에 대해 만든 앱 풀을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="ab678-235">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-235">Select **OK**.</span></span>

<span data-ttu-id="ab678-236">하위 앱에 대한 별도의 앱 풀 할당은 In-process 호스팅 모델을 사용할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="ab678-237">In-process 호스팅 모델 및 ASP.NET Core 모듈 구성에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을(를) 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="ab678-238">애플리케이션 풀</span><span class="sxs-lookup"><span data-stu-id="ab678-238">Application Pools</span></span>

<span data-ttu-id="ab678-239">앱 풀 격리는 호스팅 모델에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="ab678-240">In-process 호스팅: 앱은 별도의 앱 풀에서 실행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="ab678-241">Out-of-process 호스팅: 각 앱을 자체 앱 풀에서 실행하여 앱을 서로 격리하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="ab678-242">IIS **웹 사이트 추가** 대화 상자는 기본적으로 앱당 단일 앱 풀로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="ab678-243">**사이트 이름** 을 제공하면 텍스트가 자동으로 **애플리케이션 풀** 텍스트 상자로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="ab678-244">사이트를 추가할 때 이 사이트 이름을 사용하여 새로운 앱 풀이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="ab678-245">애플리케이션 풀 Identity</span><span class="sxs-lookup"><span data-stu-id="ab678-245">Application Pool Identity</span></span>

<span data-ttu-id="ab678-246">응용 프로그램 풀 ID 계정을 사용하면 도메인 또는 로컬 계정을 만들고 관리할 필요 없이 고유한 계정으로 앱을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="ab678-247">IIS 8.0 이상에서 IIS WAS(관리 작업자 프로세스)는 새로운 앱 풀의 이름으로 가상 계정을 만들고, 기본적으로 이 계정으로 앱 풀의 작업자 프로세스를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="ab678-248">IIS 관리 콘솔의 애플리케이션 풀에 대한 **고급 설정** 아래에서 **Identity** 가 `ApplicationPoolIdentity`를 사용하도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![애플리케이션 풀 고급 설정 대화 상자](index/_static/apppool-identity.png)

<span data-ttu-id="ab678-250">IIS 관리 프로세스는 Windows 보안 시스템에 앱 풀 이름이 포함된 보안 식별자를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="ab678-251">리소스는 이 ID를 사용하여 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="ab678-252">그러나 이 ID는 실제 사용자 계정이 아니며 Windows 사용자 관리 콘솔에 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="ab678-253">IIS 작업자 프로세스에서 앱에 대한 높은 액세스 권한이 필요한 경우 앱이 포함된 디렉터리에 대한 ACL(액세스 제어 목록)을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="ab678-254">[Windows 탐색기]를 열고 해당 하위 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="ab678-255">디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="ab678-256">**보안** 탭 아래에서 **편집** 단추, **추가** 단추를 차례로 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="ab678-257">**위치** 단추를 선택하고 시스템이 선택되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="ab678-258">**선택할 개체 이름 입력** 영역에 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}` 형식을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="ab678-259">**이름 확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-259">Select the **Check Names** button.</span></span> <span data-ttu-id="ab678-260">*DefaultAppPool* 의 경우 `IIS AppPool\DefaultAppPool`을 사용하는 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="ab678-261">**이름 확인** 단추를 선택하면 개체 이름 영역에 `DefaultAppPool` 값이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="ab678-262">개체 이름 영역에 앱 풀 이름을 직접 입력할 수는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="ab678-263">개체 이름을 확인할 때 자리 표시자 `{APP POOL NAME}`이 앱 풀 이름인 `IIS AppPool\{APP POOL NAME}` 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하기 전에 개체 이름 영역의 “IIS AppPool\"에 앱 풀 이름 “DefaultAppPool”이 추가됩니다.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="ab678-265">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-265">Select **OK**.</span></span>

   ![앱 폴더에 대한 사용자 또는 그룹 선택 대화 상자: “이름 확인”을 선택하면 개체 이름 영역에 개체 이름 “DefaultAppPool”이 표시됩니다.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="ab678-267">읽기 및 실행 권한은 기본적으로 부여됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="ab678-268">필요에 따라 추가 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="ab678-269">**ICACLS** 도구를 사용하여 명령 프롬프트에서 액세스 권한을 부여할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="ab678-270">*DefaultAppPool* 을 예로 들면, 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="ab678-271">자세한 내용은 [icacls](/windows-server/administration/windows-commands/icacls) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ab678-272">HTTP/2 지원</span><span class="sxs-lookup"><span data-stu-id="ab678-272">HTTP/2 support</span></span>

<span data-ttu-id="ab678-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html)는 다음과 같은 IIS 배포 시나리오에서 ASP.NET Core를 통해 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="ab678-274">In-Process</span><span class="sxs-lookup"><span data-stu-id="ab678-274">In-process</span></span>
  * <span data-ttu-id="ab678-275">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="ab678-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ab678-276">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="ab678-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="ab678-277">Out of Process</span><span class="sxs-lookup"><span data-stu-id="ab678-277">Out-of-process</span></span>
  * <span data-ttu-id="ab678-278">Windows Server 2016/Windows 10 이상, IIS 10 이상</span><span class="sxs-lookup"><span data-stu-id="ab678-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ab678-279">공용 에지 서버 연결은 HTTP/2를 사용하지만 [Kestrel 서버](xref:fundamentals/servers/kestrel)에 대한 역방향 프록시 연결은 HTTP/1.1을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="ab678-280">TLS 1.2 이상 연결</span><span class="sxs-lookup"><span data-stu-id="ab678-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ab678-281">In-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/2`를 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="ab678-282">Out-of-Process 배포에서 HTTP/2 연결이 설정된 경우 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)에서 `HTTP/1.1`을 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="ab678-283">In-process 및 out-of-process 호스팅 모델에 대한 자세한 내용은 <xref:host-and-deploy/aspnet-core-module>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="ab678-284">HTTP/2는 기본적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ab678-285">HTTP/2 연결이 설정되지 않은 경우 연결이 HTTP/1.1로 대체됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="ab678-286">IIS 배포가 포함된 HTTP/2 구성에 대한 자세한 내용은 [IIS의 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="ab678-287">CORS 실행 전 요청</span><span class="sxs-lookup"><span data-stu-id="ab678-287">CORS preflight requests</span></span>

<span data-ttu-id="ab678-288">이 섹션은 .NET Framework를 대상으로 하는 ASP.NET Core 앱에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="ab678-289">.NET Framework를 대상으로 하는 ASP.NET Core 앱의 경우 OPTIONS 요청은 IIS에서 기본적으로 앱에 전달되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="ab678-290">OPTIONS 요청을 전달하도록 `web.config`에서 앱의 IIS 처리기를 구성하는 방법을 알아보려면 [ASP.NET Web API 2에서 원본 간 요청을 사용하도록 설정: CORS 작동 방식](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ab678-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="ab678-291">애플리케이션 초기화 모듈 및 유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="ab678-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="ab678-292">ASP.NET Core 모듈 버전 2에서 IIS에 호스트된 경우</span><span class="sxs-lookup"><span data-stu-id="ab678-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="ab678-293">[애플리케이션 초기화 모듈](#application-initialization-module): 앱 호스팅 [In-process](xref:host-and-deploy/iis/in-process-hosting) 또는 [Out-of-process](xref:host-and-deploy/iis/out-of-process-hosting)가 작업자 프로세스를 다시 시작하거나 서버를 다시 시작할 때 자동으로 시작되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="ab678-294">[유휴 시간 제한](#idle-timeout): 앱의 호스티드 [In Process](xref:host-and-deploy/iis/in-process-hosting)가 비활성 기간 중에 시간 초과되지 않도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="ab678-295">애플리케이션 초기화 모듈</span><span class="sxs-lookup"><span data-stu-id="ab678-295">Application Initialization Module</span></span>

<span data-ttu-id="ab678-296">‘앱 호스팅 In Process 및 Out of Process에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="ab678-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="ab678-297">[IIS 애플리케이션 초기화](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)는 앱 풀이 시작되거나 재활용될 때 HTTP 요청을 앱으로 보내는 IIS 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="ab678-298">요청은 앱이 시작되도록 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-298">The request triggers the app to start.</span></span> <span data-ttu-id="ab678-299">기본적으로 IIS는 앱의 루트 URL(`/`)에 요청을 실행하여 앱을 초기화합니다(구성에 대한 자세한 내용은 [추가 리소스](#application-initialization-module-and-idle-timeout-additional-resources) 참조).</span><span class="sxs-lookup"><span data-stu-id="ab678-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="ab678-300">IIS 애플리케이션 초기화 역할 기능이 사용하도록 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="ab678-301">Windows 7 이상 데스크톱 시스템에서 IIS를 로컬로 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="ab678-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="ab678-302">**제어판** > **프로그램** > **프로그램 및 기능** > **Windows 기능 사용/사용 안 함**(화면 왼쪽)으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="ab678-303">**인터넷 정보 서비스** > **World Wide Web 서비스** > **애플리케이션 개발 기능** 을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="ab678-304">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="ab678-305">Windows Server 2008 R2 이상</span><span class="sxs-lookup"><span data-stu-id="ab678-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="ab678-306">**역할 및 기능 추가 마법사** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="ab678-307">**역할 서비스 선택** 패널에서 **애플리케이션 개발** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="ab678-308">**애플리케이션 초기화** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="ab678-309">다음 방법 중 하나를 사용하여 사이트에 대해 애플리케이션 초기화 모듈을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="ab678-310">IIS 관리자 사용</span><span class="sxs-lookup"><span data-stu-id="ab678-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="ab678-311">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="ab678-312">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="ab678-313">기본 **시작 모드** 는 `OnDemand`입니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="ab678-314">**시작 모드** 를 `AlwaysRunning`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="ab678-315">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-315">Select **OK**.</span></span>
  1. <span data-ttu-id="ab678-316">**연결** 패널에서 **사이트** 노드를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="ab678-317">앱을 마우스 오른쪽 단추로 클릭하고 **웹 사이트 관리** > **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="ab678-318">기본 **미리 로드 사용** 설정은 `False`입니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="ab678-319">**미리 로드 사용** 을 `True`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="ab678-320">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-320">Select **OK**.</span></span>

* <span data-ttu-id="ab678-321">`web.config`를 사용하여 `doAppInitAfterRestart`가 `true`로 설정된 `<applicationInitialization>` 요소를 앱의 `web.config` 파일에 있는 `<system.webServer>` 요소에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="ab678-322">유휴 시간 제한</span><span class="sxs-lookup"><span data-stu-id="ab678-322">Idle Timeout</span></span>

<span data-ttu-id="ab678-323">‘앱 호스팅 In Process 에만 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="ab678-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="ab678-324">앱의 유휴 상태를 방지하려면 IIS 관리자를 사용하여 앱 풀의 유휴 시간 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="ab678-325">**연결** 패널에서 **애플리케이션 풀** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="ab678-326">목록에서 앱의 앱 풀을 마우스 오른쪽 단추로 클릭하고 **고급 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="ab678-327">기본 **유휴 시간 제한(분)** 은 `20`분입니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="ab678-328">**유휴 시간 제한(분)** 을 `0`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="ab678-329">**확인** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-329">Select **OK**.</span></span>
1. <span data-ttu-id="ab678-330">작업자 프로세스를 재순환합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-330">Recycle the worker process.</span></span>

<span data-ttu-id="ab678-331">앱 호스팅 [Out of Process](xref:host-and-deploy/iis/out-of-process-hosting)가 시간 초과되지 않도록 하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="ab678-332">실행 상태를 유지하기 위해 외부 서비스에서 앱을 ping합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="ab678-333">앱이 백그라운드 서비스만 호스트하는 경우 IIS 호스팅을 방지하고, [Windows 서비스를 사용하여 ASP.NET Core 앱을 호스트](xref:host-and-deploy/windows-service)합니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="ab678-334">애플리케이션 초기화 모듈 및 유휴 시간 제한 추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ab678-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="ab678-335">IIS 8.0 애플리케이션 초기화</span><span class="sxs-lookup"><span data-stu-id="ab678-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="ab678-336">[애플리케이션 초기화 `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="ab678-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="ab678-337">[애플리케이션 풀의 프로세스 모델 설정 `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="ab678-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="ab678-338">모듈, 스키마 및 구성 파일 위치</span><span class="sxs-lookup"><span data-stu-id="ab678-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="ab678-339">Module</span><span class="sxs-lookup"><span data-stu-id="ab678-339">Module</span></span>

<span data-ttu-id="ab678-340">**IIS(x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="ab678-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="ab678-341">**IIS Express(x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="ab678-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="ab678-342">스키마</span><span class="sxs-lookup"><span data-stu-id="ab678-342">Schema</span></span>

<span data-ttu-id="ab678-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ab678-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="ab678-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ab678-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="ab678-345">Configuration</span><span class="sxs-lookup"><span data-stu-id="ab678-345">Configuration</span></span>

<span data-ttu-id="ab678-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="ab678-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="ab678-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="ab678-347">**IIS Express**</span></span>

* <span data-ttu-id="ab678-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="ab678-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="ab678-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="ab678-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="ab678-350">`applicationHost.config` 파일에서 `aspnetcore`를 검색하여 파일을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ab678-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
