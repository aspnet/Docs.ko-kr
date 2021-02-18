---
title: Microsoft 계정을 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: Microsoft 계정을 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱을 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: cddde7d3125dba1a250563085c366122eb26e509
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280916"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="8301f-103">Microsoft 계정을 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="8301f-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="8301f-104">이 문서에서는 인증을 위해 [Microsoft 계정과 Azure Active Directory(AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)를 사용하는 [독립 실행형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)을 만드는 방법을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-104">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="8301f-105">Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 AAD 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-105">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="8301f-106">앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD Microsoft 계정**).</span><span class="sxs-lookup"><span data-stu-id="8301f-106">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="8301f-107">**지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-107">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="8301f-108">**리디렉션 URI** 드롭다운은 **SPA(단일 페이지 애플리케이션)** 으로 설정하고 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-108">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="8301f-109">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-109">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="8301f-110">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-110">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="8301f-111">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-111">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="8301f-112">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="8301f-112">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="8301f-113">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-113">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="8301f-114">**사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-114">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="8301f-115">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-115">Select **Register**.</span></span>

<span data-ttu-id="8301f-116">애플리케이션(클라이언트) ID를 기록해 둡니다(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="8301f-116">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="8301f-117">**인증** > **플랫폼 구성** > **SPA(단일 페이지 애플리케이션)** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-117">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="8301f-118">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-118">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="8301f-119">**암시적 허용** 에서 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택 **해제** 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-119">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="8301f-120">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-120">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="8301f-121">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-121">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="8301f-122">앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD Microsoft 계정**).</span><span class="sxs-lookup"><span data-stu-id="8301f-122">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="8301f-123">**지원되는 계정 유형** 에서 **모든 조직 디렉터리의 계정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-123">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="8301f-124">**리디렉션 URI** 드롭다운은 **웹** 으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-124">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="8301f-125">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-125">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="8301f-126">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-126">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="8301f-127">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-127">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="8301f-128">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="8301f-128">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="8301f-129">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-129">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="8301f-130">**사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-130">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="8301f-131">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-131">Select **Register**.</span></span>

<span data-ttu-id="8301f-132">애플리케이션(클라이언트) ID를 기록해 둡니다(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="8301f-132">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="8301f-133">**인증** > **플랫폼 구성** > **웹** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-133">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="8301f-134">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-134">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="8301f-135">**암시적 허용** 에서는 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-135">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="8301f-136">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-136">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="8301f-137">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-137">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="8301f-138">앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-138">Create the app.</span></span> <span data-ttu-id="8301f-139">다음 명령에서 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-139">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="8301f-140">자리표시자</span><span class="sxs-lookup"><span data-stu-id="8301f-140">Placeholder</span></span>   | <span data-ttu-id="8301f-141">Azure Portal 이름</span><span class="sxs-lookup"><span data-stu-id="8301f-141">Azure portal name</span></span>       | <span data-ttu-id="8301f-142">예제</span><span class="sxs-lookup"><span data-stu-id="8301f-142">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="8301f-143">애플리케이션(클라이언트) ID</span><span class="sxs-lookup"><span data-stu-id="8301f-143">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="8301f-144">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-144">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="8301f-145">Azure Portal에서 앱의 플랫폼 구성 **리디렉션 URI** 는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-145">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="8301f-146">앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-146">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="8301f-147">앞에서 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-147">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="8301f-148">앱을 만든 후에는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-148">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="8301f-149">Microsoft 계정을 사용하여 앱에 로그인하기.</span><span class="sxs-lookup"><span data-stu-id="8301f-149">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="8301f-150">Microsoft API에 대한 액세스 토큰 요청하기.</span><span class="sxs-lookup"><span data-stu-id="8301f-150">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="8301f-151">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8301f-151">For more information, see:</span></span>
  * [<span data-ttu-id="8301f-152">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="8301f-152">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="8301f-153">[빠른 시작: 웹 API를 공개하는 애플리케이션 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8301f-153">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="8301f-154">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="8301f-154">Authentication package</span></span>

<span data-ttu-id="8301f-155">앱이 회사 또는 학교 계정을 사용하도록 만들어진 경우(`SingleOrg`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal))에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-155">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="8301f-156">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-156">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8301f-157">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-157">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="8301f-158">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-158">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="8301f-159">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-159">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="8301f-160">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="8301f-160">Authentication service support</span></span>

<span data-ttu-id="8301f-161">사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-161">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="8301f-162">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-162">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="8301f-163">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="8301f-163">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="8301f-164"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-164">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="8301f-165">앱을 구성하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-165">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="8301f-166">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-166">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="8301f-167">예:</span><span class="sxs-lookup"><span data-stu-id="8301f-167">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="8301f-168">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="8301f-168">Access token scopes</span></span>

<span data-ttu-id="8301f-169">Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-169">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="8301f-170">로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 기본 액세스 토큰 범위에 해당 범위를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-170">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="8301f-171">`AdditionalScopesToConsent`를 사용하여 추가 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="8301f-171">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="8301f-172">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8301f-172">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="8301f-173">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="8301f-173">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="8301f-174">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="8301f-174">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="8301f-175">로그인 모드</span><span class="sxs-lookup"><span data-stu-id="8301f-175">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="8301f-176">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="8301f-176">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="8301f-177">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="8301f-177">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="8301f-178">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="8301f-178">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="8301f-179">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="8301f-179">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="8301f-180">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="8301f-180">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="8301f-181">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="8301f-181">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8301f-182">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8301f-182">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="8301f-183">Authentication.MSAL JavaScript 라이브러리의 사용자 지정 버전 빌드</span><span class="sxs-lookup"><span data-stu-id="8301f-183">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="8301f-184">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="8301f-184">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="8301f-185">빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록</span><span class="sxs-lookup"><span data-stu-id="8301f-185">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="8301f-186">빠른 시작: 웹 API를 공개하는 애플리케이션 구성</span><span class="sxs-lookup"><span data-stu-id="8301f-186">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
