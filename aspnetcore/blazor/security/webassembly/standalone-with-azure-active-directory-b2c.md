---
title: Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱을 보호하는 방법을 알아봅니다.
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: dd32db8eaac70cfb3dfb3872c43c28aed6a87657
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280904"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a6360-103">Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="a6360-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a6360-104">이 문서에서는 인증을 위해 [Azure Active Directory(AAD) B2C](/azure/active-directory-b2c/overview)를 사용하는 [독립 실행형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)을 만드는 방법을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-104">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

<span data-ttu-id="a6360-105">[AAD B2C 테넌트 만들기(Azure 설명서)](/azure/active-directory-b2c/tutorial-create-tenant) 문서에 따라 테넌트를 만들거나 Azure Portal에서 앱이 사용할 기존 B2C 테넌트를 식별하세요.</span><span class="sxs-lookup"><span data-stu-id="a6360-105">Create a tenant or identify an existing B2C tenant for the app to use in the Azure portal by following the guidance in the [Create an AAD B2C tenant (Azure documentation)](/azure/active-directory-b2c/tutorial-create-tenant) article.</span></span> <span data-ttu-id="a6360-106">사용할 테넌트를 만들거나 식별한 직후 이 문서로 돌아옵니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-106">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="a6360-107">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-107">Record the following information:</span></span>

* <span data-ttu-id="a6360-108">AAD B2C 인스턴스(예: 후행 슬래시를 포함하는 `https://contoso.b2clogin.com/`): 이 인스턴스는 Azure B2C 앱 등록의 스키마 및 호스트로, Azure Portal의 **앱 등록** 페이지에서 **엔드포인트** 창을 열어 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="a6360-109">AAD B2C 주/게시자/테넌트 도메인(예: `contoso.onmicrosoft.com`): 도메인은 Azure Portal에서 등록된 앱의 **브랜딩** 블레이드에 **게시자 도메인** 으로 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-109">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a6360-110">AAD B2C 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-110">Register an AAD B2C app:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="a6360-111">**Azure Active Directory** > **앱 등록** 에서 **새 등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a6360-112">앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="a6360-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="a6360-113">**지원되는 계정 유형** 으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="a6360-114">**리디렉션 URI** 드롭다운은 **SPA(단일 페이지 애플리케이션)** 으로 설정하고 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a6360-115">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a6360-116">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a6360-117">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a6360-118">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="a6360-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a6360-119">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a6360-120">**권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="a6360-121">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-121">Select **Register**.</span></span>

<span data-ttu-id="a6360-122">애플리케이션(클라이언트) ID를 기록해 둡니다(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="a6360-122">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="a6360-123">**인증** > **플랫폼 구성** > **SPA(단일 페이지 애플리케이션)** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-123">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="a6360-124">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a6360-125">**암시적 허용** 에서 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택 **해제** 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-125">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a6360-126">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a6360-127">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-127">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="a6360-128">**Azure Active Directory** > **앱 등록** 에서 **새 등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-128">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a6360-129">앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="a6360-129">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="a6360-130">**지원되는 계정 유형** 으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-130">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="a6360-131">**리디렉션 URI** 드롭다운은 **웹** 으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-131">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a6360-132">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-132">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a6360-133">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-133">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a6360-134">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-134">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a6360-135">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="a6360-135">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a6360-136">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-136">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a6360-137">**권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-137">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="a6360-138">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-138">Select **Register**.</span></span>

<span data-ttu-id="a6360-139">애플리케이션(클라이언트) ID를 기록해 둡니다(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="a6360-139">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="a6360-140">**인증** > **플랫폼 구성** > **웹** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-140">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a6360-141">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-141">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a6360-142">**암시적 허용** 에서는 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-142">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a6360-143">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a6360-144">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-144">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a6360-145">**홈** > **Azure AD B2C** > **사용자 흐름** 에서:</span><span class="sxs-lookup"><span data-stu-id="a6360-145">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="a6360-146">가입 및 로그인 사용자 흐름 만들기</span><span class="sxs-lookup"><span data-stu-id="a6360-146">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="a6360-147">최소한 **애플리케이션 클레임** > **표시 이름** 사용자 특성을 선택하여 `LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)의 `context.User.Identity.Name`을 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-147">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="a6360-148">앱에 대해 만들어진 가입 및 로그인 사용자 흐름 이름을 기록해 둡니다(예: `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="a6360-148">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="a6360-149">빈 폴더에서 다음 명령의 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-149">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="a6360-150">자리표시자</span><span class="sxs-lookup"><span data-stu-id="a6360-150">Placeholder</span></span>                   | <span data-ttu-id="a6360-151">Azure Portal 이름</span><span class="sxs-lookup"><span data-stu-id="a6360-151">Azure portal name</span></span>               | <span data-ttu-id="a6360-152">예제</span><span class="sxs-lookup"><span data-stu-id="a6360-152">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="a6360-153">인스턴스</span><span class="sxs-lookup"><span data-stu-id="a6360-153">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="a6360-154">애플리케이션(클라이언트) ID</span><span class="sxs-lookup"><span data-stu-id="a6360-154">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="a6360-155">가입/로그인 사용자 흐름</span><span class="sxs-lookup"><span data-stu-id="a6360-155">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="a6360-156">주/게시자/테넌트 도메인</span><span class="sxs-lookup"><span data-stu-id="a6360-156">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="a6360-157">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a6360-158">Azure Portal에서 앱의 플랫폼 구성 **리디렉션 URI** 는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a6360-159">앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a6360-160">앞에서 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="a6360-161">앱을 만든 후에는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="a6360-162">AAD 사용자 계정을 사용하여 앱에 로그인하기.</span><span class="sxs-lookup"><span data-stu-id="a6360-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="a6360-163">Microsoft API에 대한 액세스 토큰 요청하기.</span><span class="sxs-lookup"><span data-stu-id="a6360-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="a6360-164">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a6360-164">For more information, see:</span></span>
  * [<span data-ttu-id="a6360-165">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="a6360-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="a6360-166">[빠른 시작: 웹 API를 공개하는 애플리케이션 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a6360-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a6360-167">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="a6360-167">Authentication package</span></span>

<span data-ttu-id="a6360-168">앱이 개별 B2C 계정을 사용하도록 만들어진 경우(`IndividualB2C`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal))에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-168">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a6360-169">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a6360-170">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a6360-171">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a6360-172">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a6360-173">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="a6360-173">Authentication service support</span></span>

<span data-ttu-id="a6360-174">사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a6360-175">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-175">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a6360-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a6360-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="a6360-177"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a6360-178">앱을 구성하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="a6360-179">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="a6360-180">예:</span><span class="sxs-lookup"><span data-stu-id="a6360-180">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="a6360-181">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="a6360-181">Access token scopes</span></span>

<span data-ttu-id="a6360-182">Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a6360-183">로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 기본 액세스 토큰 범위에 해당 범위를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a6360-184">`AdditionalScopesToConsent`를 사용하여 추가 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="a6360-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="a6360-185">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a6360-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a6360-186">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="a6360-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a6360-187">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="a6360-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="a6360-188">로그인 모드</span><span class="sxs-lookup"><span data-stu-id="a6360-188">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="a6360-189">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="a6360-189">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a6360-190">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="a6360-190">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a6360-191">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a6360-191">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a6360-192">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a6360-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a6360-193">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a6360-193">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a6360-194">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a6360-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a6360-195">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a6360-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a6360-196">Authentication.MSAL JavaScript 라이브러리의 사용자 지정 버전 빌드</span><span class="sxs-lookup"><span data-stu-id="a6360-196">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="a6360-197">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="a6360-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a6360-198">자습서: Azure Active Directory B2C 테넌트 만들기</span><span class="sxs-lookup"><span data-stu-id="a6360-198">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a6360-199">자습서: Azure Active Directory B2C에서 애플리케이션 등록</span><span class="sxs-lookup"><span data-stu-id="a6360-199">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="a6360-200">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="a6360-200">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
