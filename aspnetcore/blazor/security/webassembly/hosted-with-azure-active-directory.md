---
title: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호
author: guardrex
description: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱을 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 0542e7556b82c22a8844f4d1f4b2ba852a420246
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025071"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="26018-103">Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="26018-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="26018-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="26018-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="26018-105">이 문서에서는 인증을 위해 [AAD(Azure Active Directory )](https://azure.microsoft.com/services/active-directory/)를 사용하는 ‘[호스트형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)’을 만드는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26018-106">Visual Studio에서 만들어졌으며 AAD 조직 디렉터리에서 계정을 지원하도록 구성된 Blazor WebAssembly 앱에 대해, Visual Studio는 현재 프로젝트 생성 시 솔루션의 프로젝트 또는 Azure Portal 앱 등록을 올바르게 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure the solution's projects or the Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="26018-107">이 문제는 Visual Studio의 이후 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="26018-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="26018-108">이 문서에서는 .NET CLI `dotnet new` 명령을 사용하여 솔루션 및 Azure 앱 포털 등록을 만드는 방법과 Azure Portal에서 앱 등록을 수동으로 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="26018-108">This article shows how to create the solution and Azure app portal registrations with the .NET CLI `dotnet new` command and by manually creating the app registrations in the Azure portal.</span></span>
>
> <span data-ttu-id="26018-109">IDE가 업데이트되기 전에 Visual Studio를 사용하여 솔루션 및 Azure 앱 등록을 만들려면 **‘이 문서의 각 섹션’** 을 참조하고 Visual Studio가 솔루션을 만든 후에 앱의 구성 및 앱의 등록을 확인 또는 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-109">If you prefer to create the solution and Azure app registrations with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the apps' configurations and the apps' registrations after Visual Studio creates the solution.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="26018-110">AAD에 앱을 등록하고 솔루션 만들기</span><span class="sxs-lookup"><span data-stu-id="26018-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="26018-111">테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="26018-111">Create a tenant</span></span>

<span data-ttu-id="26018-112">[빠른 시작: 테넌트 설정](/azure/active-directory/develop/quickstart-create-new-tenant)의 지침에 따라 AAD에서 테넌트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="26018-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="26018-113">서버 API 앱 등록</span><span class="sxs-lookup"><span data-stu-id="26018-113">Register a server API app</span></span>

<span data-ttu-id="26018-114">[빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목의 지침에 따라 ‘서버 API 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="26018-115">**Azure Active Directory** > **앱 등록** 에서 **새 등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="26018-116">앱의 **이름** 을 지정합니다(예: **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="26018-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="26018-117">**지원되는 계정 유형** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="26018-118">이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="26018-119">이 시나리오에서는 ‘서버 API 앱’에 **리디렉션 URI** 가 필요하지 않으므로 드롭다운이 **웹** 으로 설정된 상태로 두고 리디렉션 URI를 입력하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="26018-120">**권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란의 선택을 취소합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="26018-121">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-121">Select **Register**.</span></span>

<span data-ttu-id="26018-122">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="26018-122">Record the following information:</span></span>

* <span data-ttu-id="26018-123">서버 API 앱 애플리케이션(클라이언트) ID(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="26018-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="26018-124">디렉터리(테넌트) ID(예: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="26018-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="26018-125">AAD 주/게시자/테넌트 도메인(예: `contoso.onmicrosoft.com`): 도메인은 Azure Portal에서 등록된 앱의 **브랜딩** 블레이드에 **게시자 도메인** 으로 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="26018-126">앱에 로그인 또는 사용자 프로필 액세스가 필요하지 않으므로 **API 사용 권한** 에서 **Microsoft Graph** > **User.Read** 사용 권한을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="26018-127">**API 표시** 에서:</span><span class="sxs-lookup"><span data-stu-id="26018-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="26018-128">**범위 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="26018-129">**저장 및 계속** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="26018-130">**범위 이름** 을 지정합니다(예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="26018-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="26018-131">**관리자 동의 표시 이름** 을 지정합니다(예: `Access API`).</span><span class="sxs-lookup"><span data-stu-id="26018-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="26018-132">**관리자 동의 설명** 을 지정합니다(예: `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="26018-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="26018-133">**상태** 가 **사용** 으로 설정되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="26018-134">**범위 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-134">Select **Add scope**.</span></span>

<span data-ttu-id="26018-135">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="26018-135">Record the following information:</span></span>

* <span data-ttu-id="26018-136">앱 ID URI(예: `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 또는 사용자가 제공하는 사용자 지정 값)</span><span class="sxs-lookup"><span data-stu-id="26018-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="26018-137">범위 이름(예: `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="26018-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="26018-138">클라이언트 앱 등록</span><span class="sxs-lookup"><span data-stu-id="26018-138">Register a client app</span></span>

<span data-ttu-id="26018-139">[빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목의 지침에 따라 *`Client`* 앱에 대해 AAD 앱을 등록하고 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="26018-140">**Azure Active Directory** > **앱 등록** 에서 **새 등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="26018-141">앱의 **이름** 을 지정합니다(예: **Blazor 클라이언트 AAD**).</span><span class="sxs-lookup"><span data-stu-id="26018-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="26018-142">**지원되는 계정 유형** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="26018-143">이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="26018-144">**리디렉션 URI** 드롭다운은 **SPA(단일 페이지 애플리케이션)** 으로 설정하고 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="26018-145">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="26018-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="26018-146">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="26018-147">IIS Express의 경우 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 *`Server`* 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="26018-148">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="26018-149">[앱 만들기](#create-the-app) 섹션에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="26018-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="26018-150">**사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="26018-151">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-151">Select **Register**.</span></span>

<span data-ttu-id="26018-152">*`Client`* 앱 애플리케이션(클라이언트) ID를 기록해 둡니다(예: `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="26018-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="26018-153">**인증** > **플랫폼 구성** > **SPA(단일 페이지 애플리케이션)** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="26018-154">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="26018-155">**암시적 허용** 에서 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택 **해제** 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-155">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="26018-156">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="26018-157">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="26018-158">**Azure Active Directory** > **앱 등록** 에서 **새 등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-158">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="26018-159">앱의 **이름** 을 지정합니다(예: **Blazor 클라이언트 AAD**).</span><span class="sxs-lookup"><span data-stu-id="26018-159">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="26018-160">**지원되는 계정 유형** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="26018-161">이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="26018-162">**리디렉션 URI** 드롭다운은 **웹** 으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="26018-163">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="26018-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="26018-164">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="26018-165">IIS Express의 경우 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 *`Server`* 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="26018-166">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="26018-167">[앱 만들기](#create-the-app) 섹션에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="26018-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="26018-168">**사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="26018-169">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-169">Select **Register**.</span></span>

<span data-ttu-id="26018-170">*`Client`* 앱 애플리케이션(클라이언트) ID를 기록해 둡니다(예: `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="26018-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="26018-171">**인증** > **플랫폼 구성** > **웹** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-171">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="26018-172">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="26018-173">**암시적 허용** 에서는 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-173">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="26018-174">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="26018-175">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="26018-176">**API 사용 권한** 에서:</span><span class="sxs-lookup"><span data-stu-id="26018-176">In **API permissions**:</span></span>

1. <span data-ttu-id="26018-177">앱에 **Microsoft Graph** > **User.Read** 사용 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="26018-178">**사용 권한 추가** 를 선택하고 **내 API** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="26018-179">**이름** 열에서 ‘서버 API 앱’을 선택합니다(예: **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="26018-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="26018-180">**API** 목록을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="26018-180">Open the **API** list.</span></span>
1. <span data-ttu-id="26018-181">API에 대한 액세스를 사용하도록 설정합니다(예: `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="26018-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="26018-182">**권한 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="26018-183">**{테넌트 이름}에 대한 관리자 동의 허용** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="26018-184">**예** 를 선택하여 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="26018-185">앱 만들기</span><span class="sxs-lookup"><span data-stu-id="26018-185">Create the app</span></span>

<span data-ttu-id="26018-186">빈 폴더에서 다음 명령의 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="26018-187">자리표시자</span><span class="sxs-lookup"><span data-stu-id="26018-187">Placeholder</span></span>                  | <span data-ttu-id="26018-188">Azure Portal 이름</span><span class="sxs-lookup"><span data-stu-id="26018-188">Azure portal name</span></span>                                     | <span data-ttu-id="26018-189">예제</span><span class="sxs-lookup"><span data-stu-id="26018-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="26018-190">*`Client`* 앱의 애플리케이션(클라이언트) ID</span><span class="sxs-lookup"><span data-stu-id="26018-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="26018-191">범위 이름</span><span class="sxs-lookup"><span data-stu-id="26018-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="26018-192">서버 API 앱의 애플리케이션(클라이언트) ID</span><span class="sxs-lookup"><span data-stu-id="26018-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="26018-193">애플리케이션 ID URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="26018-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="26018-194">주/게시자/테넌트 도메인</span><span class="sxs-lookup"><span data-stu-id="26018-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="26018-195">디렉터리(테넌트) ID</span><span class="sxs-lookup"><span data-stu-id="26018-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="26018-196">&dagger;Blazor WebAssembly 템플릿은 `dotnet new` 명령에 전달된 앱 ID URI 인수에 `api://`의 구성표를 자동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-196">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="26018-197">`{SERVER API APP ID URI}` 자리 표시자의 앱 ID URI를 제공할 때 구성표가 `api://`인 경우 앞의 테이블에 나온 것처럼 인수에서 구성표(`api://`)를 제거하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="26018-198">앱 ID URI가 사용자 지정 값이거나 다른 구성표(예를 들어 `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`와 유사한 신뢰할 수 없는 게시자 도메인의 `https://`)를 갖는 경우 기본 범위 URI를 수동으로 업데이트하고 템플릿이 *`Client`* 앱을 만든 후 `api://` 구성표를 제거해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="26018-199">자세한 내용은 [액세스 토큰 범위](#access-token-scopes) 섹션의 참고를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="26018-200">이러한 시나리오를 해결하기 위해 ASP.NET Core의 이후 릴리스에서 Blazor WebAssembly 템플릿이 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-200">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="26018-201">자세한 내용은 [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-201">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="26018-202">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="26018-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26018-203">확인되지 않은 게시자 도메인에서 Azure 테넌트를 사용하는 경우 구성 변경이 필요할 수 있으며 이는 [앱 설정](#app-settings) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="26018-204">확인되지 않은 게시자 도메인에서 Azure 테넌트를 사용하는 경우 구성 변경이 필요할 수 있으며 이는 [액세스 토큰 범위](#access-token-scopes) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="26018-205">Azure Portal에서 *`Client`* 앱의 플랫폼 구성 **리디렉션 URI** 는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="26018-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="26018-206">*`Client`* 앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 ‘서버 API 앱’ 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="26018-207">앞에서 *`Client`* 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 *`Client`* 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="26018-208">*`Server`* 앱 구성</span><span class="sxs-lookup"><span data-stu-id="26018-208">*`Server`* app configuration</span></span>

<span data-ttu-id="26018-209">‘이 섹션은 솔루션의 **`Server`** 앱에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="26018-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="26018-210">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="26018-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="26018-211">Microsoft Identity 플랫폼을 통한 ASP.NET Core Web API에 대한 호출의 권한 부여 및 인증 지원은 다음 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="26018-212">자리 표시자 `{VERSION}`의 경우 앱의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 NuGet.org의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="26018-213">ASP.NET Core Web API에 대한 호출의 인증 및 권한 부여 지원은 [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) 패키지에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="26018-214">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="26018-215">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="26018-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="26018-216">`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정하고 JWT 전달자 처리기를 기본 인증 메서드로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="26018-217"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> 메서드는 Microsoft Identity 플랫폼 v2.0을 사용하여 웹 API를 보호하도록 서비스를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-217">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="26018-218">이 메서드에는 인증 옵션을 초기화하는 데 필요한 설정을 포함하는 앱 구성의 `AzureAd` 섹션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="26018-219">`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정하고 JWT 전달자 처리기를 기본 인증 메서드로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="26018-220"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> 메서드는 JWT 전달자 처리기에서 Azure Active Directory에서 내보낸 토큰의 유효성을 검사하는 데 필요한 특정 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="26018-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 및 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>은 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="26018-222">앱이 수신 요청에 대해 토큰의 구문 분석 및 유효성 검사를 시도하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="26018-223">올바른 자격 증명 없이 보호된 리소스에 액세스하려는 요청은 모두 실패하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="26018-224">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="26018-224">User.Identity.Name</span></span>

<span data-ttu-id="26018-225">기본적으로 *`Server`* 앱 API는 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 클레임 유형의 값을 사용하여 `User.Identity.Name`을 채웁니다(예: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="26018-225">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="26018-226">앱이 `name` 클레임 유형으로부터 값을 받도록 구성하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>의 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-226">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="26018-227">앱 설정</span><span class="sxs-lookup"><span data-stu-id="26018-227">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="26018-228">`appsettings.json` 파일은 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 전달자 처리기를 구성하기 위한 옵션을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-228">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="26018-229">예:</span><span class="sxs-lookup"><span data-stu-id="26018-229">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="26018-230">`appsettings.json` 파일은 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 전달자 처리기를 구성하기 위한 옵션을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-230">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="26018-231">예:</span><span class="sxs-lookup"><span data-stu-id="26018-231">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="26018-232">WeatherForecast 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="26018-232">WeatherForecast controller</span></span>

<span data-ttu-id="26018-233">WeatherForecast 컨트롤러(*Controllers/WeatherForecastController.cs*)는 컨트롤러에 적용된 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 사용하여, 보호된 API를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-233">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="26018-234">다음과 같은 사항을 이해하는 것이 **중요합니다**.</span><span class="sxs-lookup"><span data-stu-id="26018-234">It's **important** to understand that:</span></span>

* <span data-ttu-id="26018-235">이 API 컨트롤러의 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 무단 액세스로부터 이 API 컨트롤러를 보호하는 유일한 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="26018-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="26018-236">Blazor WebAssembly 앱에서 사용되는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 앱이 올바르게 작동하려면 사용자에게 권한이 부여되어야 한다는 힌트만 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-236">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="26018-237">*`Client`* 앱 구성</span><span class="sxs-lookup"><span data-stu-id="26018-237">*`Client`* app configuration</span></span>

<span data-ttu-id="26018-238">‘이 섹션은 솔루션의 **`Client`** 앱에 적용됩니다.’</span><span class="sxs-lookup"><span data-stu-id="26018-238">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="26018-239">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="26018-239">Authentication package</span></span>

<span data-ttu-id="26018-240">앱이 회사 또는 학교 계정을 사용하도록 만들어진 경우(`SingleOrg`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal))에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-240">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="26018-241">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-241">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="26018-242">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-242">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="26018-243">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-243">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="26018-244">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-244">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="26018-245">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="26018-245">Authentication service support</span></span>

<span data-ttu-id="26018-246">서버 프로젝트로 요청을 전송할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스에 대한 지원이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="26018-246">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="26018-247">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="26018-247">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="26018-248">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="26018-248">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="26018-249">사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="26018-249">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="26018-250">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-250">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="26018-251">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="26018-251">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="26018-252"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-252">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="26018-253">앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-253">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="26018-254">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-254">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="26018-255">예:</span><span class="sxs-lookup"><span data-stu-id="26018-255">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="26018-256">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="26018-256">Access token scopes</span></span>

<span data-ttu-id="26018-257">기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위 목록을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="26018-257">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="26018-258">기본적으로 로그인 요청에 포함되어 있음.</span><span class="sxs-lookup"><span data-stu-id="26018-258">Included by default in the sign in request.</span></span>
* <span data-ttu-id="26018-259">인증 직후에 액세스 토큰을 프로비저닝하는 데 사용됨.</span><span class="sxs-lookup"><span data-stu-id="26018-259">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="26018-260">모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-260">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="26018-261">필요에 따라 추가 API 앱에 대한 추가 범위가 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-261">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="26018-262">Blazor WebAssembly 템플릿은 `dotnet new` 명령에 전달된 앱 ID URI 인수에 `api://`의 구성표를 자동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-262">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="26018-263">Blazor 프로젝트 템플릿에서 앱을 생성할 때 기본 액세스 토큰 범위의 값이 Azure Portal에 제공한 올바른 사용자 지정 앱 ID URI 값 또는 다음 형식 중 **하나** 인 값을 사용하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-263">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="26018-264">디렉터리의 게시자 도메인을 **신뢰할 수 있는** 경우 기본 액세스 토큰 범위는 일반적으로 다음 예제와 비슷한 값입니다. 여기서 `API.Access`는 기본 범위 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="26018-264">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="26018-265">이중 구성표(`api://api://...`)의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-265">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="26018-266">이중 구성표가 있는 경우 값에서 첫 번째 `api://` 구성표를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-266">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="26018-267">디렉터리의 게시자 도메인을 **신뢰할 수 없는** 경우 기본 액세스 토큰 범위는 일반적으로 다음 예제와 비슷한 값입니다. 여기서 `API.Access`는 기본 범위 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="26018-267">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="26018-268">추가 `api://` 구성표(`api://https://contoso.onmicrosoft.com/...`)의 값을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-268">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="26018-269">추가 `api://` 구성표가 있는 경우 값에서 `api://` 구성표를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-269">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="26018-270">이러한 시나리오를 해결하기 위해 ASP.NET Core의 이후 릴리스에서 Blazor WebAssembly 템플릿이 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="26018-270">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="26018-271">자세한 내용은 [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-271">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="26018-272">`AdditionalScopesToConsent`를 사용하여 추가 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-272">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="26018-273">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="26018-273">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="26018-274">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="26018-274">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="26018-275">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="26018-275">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="26018-276">로그인 모드</span><span class="sxs-lookup"><span data-stu-id="26018-276">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="26018-277">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="26018-277">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="26018-278">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="26018-278">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="26018-279">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26018-279">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="26018-280">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26018-280">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="26018-281">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26018-281">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="26018-282">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26018-282">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="26018-283">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="26018-283">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="26018-284">앱 실행</span><span class="sxs-lookup"><span data-stu-id="26018-284">Run the app</span></span>

<span data-ttu-id="26018-285">서버 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-285">Run the app from the Server project.</span></span> <span data-ttu-id="26018-286">Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-286">When using Visual Studio, either:</span></span>

* <span data-ttu-id="26018-287">도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-287">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="26018-288">**솔루션 탐색기** 에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="26018-288">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="26018-289">추가 자료</span><span class="sxs-lookup"><span data-stu-id="26018-289">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="26018-290">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="26018-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="26018-291">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="26018-291">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
