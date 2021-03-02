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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552530"
---
## <a name="troubleshoot"></a><span data-ttu-id="9a3c7-101">문제 해결</span><span class="sxs-lookup"><span data-stu-id="9a3c7-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="9a3c7-102">일반 오류</span><span class="sxs-lookup"><span data-stu-id="9a3c7-102">Common errors</span></span>

* <span data-ttu-id="9a3c7-103">AAD에 대한 권한이 없는 클라이언트</span><span class="sxs-lookup"><span data-stu-id="9a3c7-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="9a3c7-104">정보: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] 권한 부여에 실패했습니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="9a3c7-105">다음 요구 사항이 충족되지 않았습니다. DenyAnonymousAuthorizationRequirement: 인증된 사용자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="9a3c7-106">AAD에서 로그인 콜백 오류:</span><span class="sxs-lookup"><span data-stu-id="9a3c7-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="9a3c7-107">오류: `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="9a3c7-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="9a3c7-108">설명: `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="9a3c7-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="9a3c7-109">오류를 해결하려면:</span><span class="sxs-lookup"><span data-stu-id="9a3c7-109">To resolve the error:</span></span>

  1. <span data-ttu-id="9a3c7-110">Azure Portal에서 [앱의 매니페스트](/azure/active-directory/develop/reference-app-manifest)에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="9a3c7-111">[`allowPublicClient` 특성](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)을 `null` 또는 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-111">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="9a3c7-112">Cookie 및 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="9a3c7-112">Cookies and site data</span></span>

<span data-ttu-id="9a3c7-113">Cookie 및 사이트 데이터가 앱을 업데이트할 때에도 유지되어 테스트 및 문제 해결에 방해가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="9a3c7-114">앱 코드를 변경하거나 공급자를 사용하여 사용자 계정을 변경하거나 공급자 앱 구성을 변경하는 경우 다음을 지우세요.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="9a3c7-115">사용자 로그인 cookie</span><span class="sxs-lookup"><span data-stu-id="9a3c7-115">User sign-in cookies</span></span>
* <span data-ttu-id="9a3c7-116">앱 cookie</span><span class="sxs-lookup"><span data-stu-id="9a3c7-116">App cookies</span></span>
* <span data-ttu-id="9a3c7-117">캐시되고 저장된 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="9a3c7-117">Cached and stored site data</span></span>

<span data-ttu-id="9a3c7-118">남겨진 cookie 및 사이트 데이터로 인해 테스트 및 문제 해결이 지장을 받지 않도록 하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="9a3c7-119">브라우저 구성</span><span class="sxs-lookup"><span data-stu-id="9a3c7-119">Configure a browser</span></span>
  * <span data-ttu-id="9a3c7-120">브라우저에서 브라우저를 닫을 때마다 모든 cookie 및 사이트 데이터를 삭제하도록 구성할 수 있는지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="9a3c7-121">앱, 테스트 사용자 또는 공급자 구성을 변경할 때 수동으로 또는 IDE를 통해 브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="9a3c7-122">사용자 지정 명령을 사용하여 Visual Studio에서 시크릿 또는 프라이빗 모드로 브라우저를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="9a3c7-123">Visual Studio의 **실행** 단추를 통해 **브라우저 선택** 대화 상자를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="9a3c7-124">**추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="9a3c7-125">**프로그램** 필드에서 브라우저의 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="9a3c7-126">다음 실행 파일 경로는 Windows 10에서 일반적인 설치 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="9a3c7-127">브라우저가 다른 위치에 설치되어 있거나 Windows 10을 사용하지 않는 경우 브라우저 실행 파일의 경로를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="9a3c7-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="9a3c7-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="9a3c7-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="9a3c7-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="9a3c7-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="9a3c7-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="9a3c7-131">**인수** 필드에 브라우저에서 시크릿 또는 프라이빗 모드로 열 때 사용하는 명령줄 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="9a3c7-132">일부 브라우저에는 앱의 URL이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="9a3c7-133">Microsoft Edge: `-inprivate`을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="9a3c7-134">Google Chrome: `--incognito --new-window {URL}`를 사용합니다. 여기서 자리 표시자 `{URL}`은 열려는 URL입니다(예: `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="9a3c7-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="9a3c7-135">Mozilla Firefox: `-private -url {URL}`을 사용합니다. 여기서 자리 표시자 `{URL}`은 열려는 URL입니다(예: `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="9a3c7-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="9a3c7-136">**이름** 필드에 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="9a3c7-137">예: `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="9a3c7-138">**확인** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="9a3c7-139">앱 테스트를 반복할 때마다 브라우저 프로필을 선택할 필요가 없도록 하려면 **기본값으로 설정** 단추를 사용하여 프로필을 기본값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="9a3c7-140">앱, 테스트 사용자 또는 공급자 구성을 변경할 때 IDE를 통해 브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="9a3c7-141">서버 앱 실행</span><span class="sxs-lookup"><span data-stu-id="9a3c7-141">Run the Server app</span></span>

<span data-ttu-id="9a3c7-142">호스트된 Blazor 앱을 테스트하고 문제를 해결할 때 **`Server`** 프로젝트에서 앱을 실행하고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="9a3c7-143">예를 들어 Visual Studio에서 다음 방법 중 하나를 사용하여 앱을 시작하기 전에 **솔루션 탐색기** 에서 서버 프로젝트가 강조 표시되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="9a3c7-144">**실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-144">Select the **Run** button.</span></span>
* <span data-ttu-id="9a3c7-145">메뉴에서 **디버그** > **디버깅 시작** 을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="9a3c7-146"><kbd>F5</kbd>키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="9a3c7-147">JWT(JSON Web Token)의 콘텐츠 검사</span><span class="sxs-lookup"><span data-stu-id="9a3c7-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="9a3c7-148">JWT(JSON Web Token)를 디코딩하려면 Microsoft의 [jwt.ms](https://jwt.ms/) 도구를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="9a3c7-149">UI의 값은 브라우저에 남겨지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9a3c7-149">Values in the UI never leave your browser.</span></span>
