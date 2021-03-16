---
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
ms.openlocfilehash: f58da78475d65cbb70b0b177e1b0443535e97d55
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102402306"
---
## <a name="troubleshoot"></a><span data-ttu-id="ac373-101">문제 해결</span><span class="sxs-lookup"><span data-stu-id="ac373-101">Troubleshoot</span></span>

### <a name="common-errors"></a><span data-ttu-id="ac373-102">일반 오류</span><span class="sxs-lookup"><span data-stu-id="ac373-102">Common errors</span></span>

* <span data-ttu-id="ac373-103">앱 또는 IP(Identity 공급자)의 잘못된 구성</span><span class="sxs-lookup"><span data-stu-id="ac373-103">Misconfiguration of the app or Identity Provider (IP)</span></span>

  <span data-ttu-id="ac373-104">가장 일반적인 오류는 잘못된 구성으로 인해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-104">The most common errors are caused by incorrect configuration.</span></span> <span data-ttu-id="ac373-105">다음은 몇 가지 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-105">The following are a few examples:</span></span>
  
  * <span data-ttu-id="ac373-106">시나리오의 요구 사항에 따라, 누락되거나 잘못된 권한, 인스턴스, 테넌트 ID, 테넌트 도메인, 클라이언트 ID 또는 리디렉션 URI 때문에 앱에서 클라이언트를 인증하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-106">Depending on the requirements of the scenario, a missing or incorrect Authority, Instance, Tenant ID, Tenant domain, Client ID, or Redirect URI prevents an app from authenticating clients.</span></span>
  * <span data-ttu-id="ac373-107">액세스 토큰 범위가 잘못되어 클라이언트가 서버 웹 API 엔드포인트에 액세스하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-107">An incorrect access token scope prevents clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="ac373-108">서버 API 권한이 잘못되거나 누락되어 클라이언트가 서버 웹 API 엔드포인트에 액세스할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-108">Incorrect or missing server API permissions prevent clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="ac373-109">Identity 공급자 앱 등록의 리디렉션 URI에 구성된 것과 다른 포트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-109">Running the app at a different port than is configured in the Redirect URI of the Identity Provider's app registration.</span></span>
  
  <span data-ttu-id="ac373-110">이 문서 지침의 구성 섹션에서는 올바른 구성의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-110">Configuration sections of this article's guidance show examples of the correct configuration.</span></span> <span data-ttu-id="ac373-111">앱 및 IP 구성을 찾는 문서의 각 섹션을 주의 깊게 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="ac373-111">Carefully check each section of the article looking for app and IP misconfiguration.</span></span>
  
  <span data-ttu-id="ac373-112">구성이 올바르게 표시되면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-112">If the configuration appears correct:</span></span>
  
  * <span data-ttu-id="ac373-113">애플리케이션 로그를 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-113">Analyze application logs.</span></span>
  * <span data-ttu-id="ac373-114">브라우저의 개발자 도구를 사용하여 클라이언트 앱과 IP 또는 서버 앱 간의 네트워크 트래픽을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-114">Examine the network traffic between the client app and the IP or server app with the browser's developer tools.</span></span> <span data-ttu-id="ac373-115">종종 정확한 오류 메시지 또는 문제의 원인에 대한 단서가 있는 메시지가 요청을 수행한 후 IP 또는 서버 앱에 의해 클라이언트로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-115">Often, an exact error message or a message with a clue to what's causing the problem is returned to the client by the IP or server app after making a request.</span></span> <span data-ttu-id="ac373-116">개발자 도구 지침은 다음 문서에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-116">Developer tools guidance is found in the following articles:</span></span>

    * <span data-ttu-id="ac373-117">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network)(Google 설명서)</span><span class="sxs-lookup"><span data-stu-id="ac373-117">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (Google documentation)</span></span>
    * [<span data-ttu-id="ac373-118">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="ac373-118">Microsoft Edge</span></span>](/microsoft-edge/devtools-guide-chromium/network/)
    * <span data-ttu-id="ac373-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor)(Mozilla 설명서)</span><span class="sxs-lookup"><span data-stu-id="ac373-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (Mozilla documentation)</span></span>

  * <span data-ttu-id="ac373-120">문제가 발생한 위치에 따라 클라이언트를 인증하거나 서버 웹 API에 액세스하는 데 사용되는 JWT(JSON Web Token)의 콘텐츠를 디코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-120">Decode the contents of a JSON Web Token (JWT) used for authenticating a client or accessing a server web API, depending on where the problem is occurring.</span></span> <span data-ttu-id="ac373-121">자세한 내용은 [JWT(JSON Web Token)의 콘텐츠 검사](#inspect-the-content-of-a-json-web-token-jwt)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac373-121">For more information, see [Inspect the content of a JSON Web Token (JWT)](#inspect-the-content-of-a-json-web-token-jwt).</span></span>
  
  <span data-ttu-id="ac373-122">문서 작업 팀은 설명서 피드백 및 문서의 버그에 응답하지만(**이 페이지** 의 피드백 섹션에서 문제 열기) 제품 지원을 제공할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-122">The documenation team responds to document feedback and bugs in articles (open an issue from the **This page** feedback section) but is unable to provide product support.</span></span> <span data-ttu-id="ac373-123">앱 문제 해결을 지원하기 위해 몇 가지 퍼블릭 지원 포럼을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-123">Several public support forums are available to assist with troubleshooting an app.</span></span> <span data-ttu-id="ac373-124">다음 방법을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-124">We recommend the following:</span></span>
  
  * [<span data-ttu-id="ac373-125">Stack Overflow(태그: `blazor`)</span><span class="sxs-lookup"><span data-stu-id="ac373-125">Stack Overflow (tag: `blazor`)</span></span>](https://stackoverflow.com/questions/tagged/blazor)
  * [<span data-ttu-id="ac373-126">ASP.NET Core Slack 팀</span><span class="sxs-lookup"><span data-stu-id="ac373-126">ASP.NET Core Slack Team</span></span>](http://tattoocoder.com/aspnet-slack-sign-up/)
  * <span data-ttu-id="ac373-127">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span><span class="sxs-lookup"><span data-stu-id="ac373-127">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span></span>
  
  <span data-ttu-id="ac373-128">중요하지 않고 보안이나 기밀이 아니며 재현 가능한 프레임워크 버그 보고서의 경우 [ASP.NET Core 제품 단위에서 문제를 여세요](https://github.com/dotnet/aspnetcore/issues).</span><span class="sxs-lookup"><span data-stu-id="ac373-128">For non-security, non-sensitive, and non-confidential reproducible framework bug reports, [open an issue with the ASP.NET Core product unit](https://github.com/dotnet/aspnetcore/issues).</span></span> <span data-ttu-id="ac373-129">문제의 원인을 철저하게 조사했으며 자신의 노력과 퍼블릭 지원 포럼에서 커뮤니티의 도움을 받아도 해결할 수 없는 경우에만 제품 단위에서 문제를 열고 그전에는 열지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ac373-129">Don't open an issue with the product unit until you've thoroughly investigated the cause of a problem and can't resolve it on your own and with the help of the community on a public support forum.</span></span> <span data-ttu-id="ac373-130">제품 단위는 단순한 구성 오류 또는 타사 서비스와 관련된 사용 사례로 인해 손상된 개별 앱의 문제를 해결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-130">The product unit isn't able to troubleshoot individual apps that are broken due to simple misconfiguration or use cases involving third-party services.</span></span> <span data-ttu-id="ac373-131">신고서가 본질적으로 중요하거나 기밀이거나 공격자가 악용할 수 있는 제품의 잠재적인 보안 결함을 설명하는 경우 [보안 문제 및 버그 신고(dotnet/aspnetcore GitHub 리포지토리)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac373-131">If a report is sensitive or confidential in nature or describes a potential security flaw in the product that attackers may exploit, see [Reporting security issues and bugs (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="ac373-132">AAD에 대한 권한이 없는 클라이언트</span><span class="sxs-lookup"><span data-stu-id="ac373-132">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="ac373-133">정보: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] 권한 부여에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-133">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="ac373-134">다음 요구 사항이 충족되지 않았습니다. DenyAnonymousAuthorizationRequirement: 인증된 사용자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-134">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="ac373-135">AAD에서 로그인 콜백 오류:</span><span class="sxs-lookup"><span data-stu-id="ac373-135">Login callback error from AAD:</span></span>

  * <span data-ttu-id="ac373-136">오류: `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="ac373-136">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="ac373-137">설명: `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="ac373-137">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="ac373-138">오류를 해결하려면:</span><span class="sxs-lookup"><span data-stu-id="ac373-138">To resolve the error:</span></span>

  1. <span data-ttu-id="ac373-139">Azure Portal에서 [앱의 매니페스트](/azure/active-directory/develop/reference-app-manifest)에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-139">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="ac373-140">[`allowPublicClient` 특성](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)을 `null` 또는 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-140">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="ac373-141">Cookie 및 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="ac373-141">Cookies and site data</span></span>

<span data-ttu-id="ac373-142">Cookie 및 사이트 데이터가 앱을 업데이트할 때에도 유지되어 테스트 및 문제 해결에 방해가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-142">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="ac373-143">앱 코드를 변경하거나 공급자를 사용하여 사용자 계정을 변경하거나 공급자 앱 구성을 변경하는 경우 다음을 지우세요.</span><span class="sxs-lookup"><span data-stu-id="ac373-143">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="ac373-144">사용자 로그인 cookie</span><span class="sxs-lookup"><span data-stu-id="ac373-144">User sign-in cookies</span></span>
* <span data-ttu-id="ac373-145">앱 cookie</span><span class="sxs-lookup"><span data-stu-id="ac373-145">App cookies</span></span>
* <span data-ttu-id="ac373-146">캐시되고 저장된 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="ac373-146">Cached and stored site data</span></span>

<span data-ttu-id="ac373-147">남겨진 cookie 및 사이트 데이터로 인해 테스트 및 문제 해결이 지장을 받지 않도록 하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-147">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="ac373-148">브라우저 구성</span><span class="sxs-lookup"><span data-stu-id="ac373-148">Configure a browser</span></span>
  * <span data-ttu-id="ac373-149">브라우저에서 브라우저를 닫을 때마다 모든 cookie 및 사이트 데이터를 삭제하도록 구성할 수 있는지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-149">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="ac373-150">앱, 테스트 사용자 또는 공급자 구성을 변경할 때 수동으로 또는 IDE를 통해 브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-150">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="ac373-151">사용자 지정 명령을 사용하여 Visual Studio에서 시크릿 또는 프라이빗 모드로 브라우저를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-151">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="ac373-152">Visual Studio의 **실행** 단추를 통해 **브라우저 선택** 대화 상자를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-152">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="ac373-153">**추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-153">Select the **Add** button.</span></span>
  * <span data-ttu-id="ac373-154">**프로그램** 필드에서 브라우저의 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-154">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="ac373-155">다음 실행 파일 경로는 Windows 10에서 일반적인 설치 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-155">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="ac373-156">브라우저가 다른 위치에 설치되어 있거나 Windows 10을 사용하지 않는 경우 브라우저 실행 파일의 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-156">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="ac373-157">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="ac373-157">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="ac373-158">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="ac373-158">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="ac373-159">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="ac373-159">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="ac373-160">**인수** 필드에 브라우저에서 시크릿 또는 프라이빗 모드로 열 때 사용하는 명령줄 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-160">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="ac373-161">일부 브라우저에는 앱의 URL이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-161">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="ac373-162">Microsoft Edge: `-inprivate`을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="ac373-162">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="ac373-163">Google Chrome: `--incognito --new-window {URL}`를 사용합니다. 여기서 자리 표시자 `{URL}`은 열려는 URL입니다(예: `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="ac373-163">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="ac373-164">Mozilla Firefox: `-private -url {URL}`을 사용합니다. 여기서 자리 표시자 `{URL}`은 열려는 URL입니다(예: `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="ac373-164">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="ac373-165">**이름** 필드에 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-165">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="ac373-166">예: `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="ac373-166">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="ac373-167">**확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-167">Select the **OK** button.</span></span>
  * <span data-ttu-id="ac373-168">앱 테스트를 반복할 때마다 브라우저 프로필을 선택할 필요가 없도록 하려면 **기본값으로 설정** 단추를 사용하여 프로필을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-168">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="ac373-169">앱, 테스트 사용자 또는 공급자 구성을 변경할 때 IDE를 통해 브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-169">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="app-upgrades"></a><span data-ttu-id="ac373-170">앱 업그레이드</span><span class="sxs-lookup"><span data-stu-id="ac373-170">App upgrades</span></span>

<span data-ttu-id="ac373-171">개발 컴퓨터의 .NET Core SDK 또는 앱 내의 패키지 버전을 업그레이드하거나 앱 내 패키지 버전을 변경한 후 즉시 작동 중인 앱에서 오류가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-171">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="ac373-172">경우에 따라 중요한 업그레이드를 수행할 때 일관되지 않은 패키지로 인해 응용 프로그램이 중단될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-172">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="ac373-173">이러한 대부분의 문제는 다음 지침에 따라 수정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-173">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="ac373-174">명령 셸에서 [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals)를 실행하여 로컬 시스템의 NuGet 패키지 캐시를 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-174">Clear the local system's NuGet package caches by executing [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>
1. <span data-ttu-id="ac373-175">프로젝트의 `bin` 및 `obj` 폴더를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-175">Delete the project's `bin` and `obj` folders.</span></span>
1. <span data-ttu-id="ac373-176">프로젝트를 복원하고 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-176">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="ac373-177">앱을 다시 배포하기 전에 서버의 배포 폴더에 있는 모든 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-177">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

> [!NOTE]
> <span data-ttu-id="ac373-178">앱의 대상 프레임워크와 호환되지 않는 패키지 버전의 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-178">Use of package versions incompatible with the app's target framework isn't supported.</span></span> <span data-ttu-id="ac373-179">패키지에 대한 자세한 내용은 [NuGet 갤러리](https://www.nuget.org) 또는 [FuGet 패키지 탐색기](https://www.fuget.org)를 사용하세요.</span><span class="sxs-lookup"><span data-stu-id="ac373-179">For information on a package, use the [NuGet Gallery](https://www.nuget.org) or [FuGet Package Explorer](https://www.fuget.org).</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="ac373-180">서버 앱 실행</span><span class="sxs-lookup"><span data-stu-id="ac373-180">Run the Server app</span></span>

<span data-ttu-id="ac373-181">호스트된 Blazor 솔루션을 테스트하고 문제를 해결할 때 **`Server`** 프로젝트에서 앱을 실행하고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-181">When testing and troubleshooting a hosted Blazor solution, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="ac373-182">예를 들어 Visual Studio에서 다음 방법 중 하나를 사용하여 앱을 시작하기 전에 **솔루션 탐색기** 에서 서버 프로젝트가 강조 표시되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-182">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="ac373-183">**실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-183">Select the **Run** button.</span></span>
* <span data-ttu-id="ac373-184">메뉴에서 **디버그** > **디버깅 시작** 을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-184">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="ac373-185"><kbd>F5</kbd>키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-185">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="ac373-186">JWT(JSON Web Token)의 콘텐츠 검사</span><span class="sxs-lookup"><span data-stu-id="ac373-186">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="ac373-187">JWT(JSON Web Token)를 디코딩하려면 Microsoft의 [jwt.ms](https://jwt.ms/) 도구를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-187">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="ac373-188">UI의 값은 브라우저에 남겨지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac373-188">Values in the UI never leave your browser.</span></span>

<span data-ttu-id="ac373-189">인코딩된 JWT 예제(표시를 위해 축약됨):</span><span class="sxs-lookup"><span data-stu-id="ac373-189">Example encoded JWT (shortened for display):</span></span>

> <span data-ttu-id="ac373-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span><span class="sxs-lookup"><span data-stu-id="ac373-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span></span>

<span data-ttu-id="ac373-191">Azure AAD B2C에 대해 인증하는 앱용 도구에 의해 디코딩된 JWT 예제:</span><span class="sxs-lookup"><span data-stu-id="ac373-191">Example JWT decoded by the tool for an app that authenticates against Azure AAD B2C:</span></span>

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1610059429,
  "nbf": 1610055829,
  "ver": "1.0",
  "iss": "https://mysiteb2c.b2clogin.com/5cc15ea8-a296-4aa3-97e4-226dcc9ad298/v2.0/",
  "sub": "5ee963fb-24d6-4d72-a1b6-889c6e2c7438",
  "aud": "70bde375-fce3-4b82-984a-b247d823a03f",
  "nonce": "b2641f54-8dc4-42ca-97ea-7f12ff4af871",
  "iat": 1610055829,
  "auth_time": 1610055822,
  "idp": "idp.com",
  "tfp": "B2C_1_signupsignin"
}.[Signature]
```
