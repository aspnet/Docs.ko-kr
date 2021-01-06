---
title: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱을 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 557c700ffd24d4d6c85848ad360690d583e05321
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854480"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="2b479-103">Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="2b479-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="2b479-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b479-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2b479-105">이 문서에서는 인증을 위해 [Azure Active Directory(AAD)](https://azure.microsoft.com/services/active-directory/)를 사용하는 [독립 실행형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)을 만드는 방법을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="2b479-106">Visual Studio에서 만들어졌으며 AAD 조직 디렉터리에서 계정을 지원하도록 구성된 Blazor WebAssembly 앱에 대해, Visual Studio는 현재 프로젝트 생성 시 Azure Portal 앱 등록을 올바르게 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="2b479-107">이 문제는 Visual Studio의 이후 릴리스에서 해결될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="2b479-108">이 문서에서는 .NET CLI `dotnet new` 명령을 사용하여 솔루션 및 Azure 앱 포털 등록을 만드는 방법과 Azure Portal에서 앱 등록을 수동으로 만드는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-108">This article shows how to create the solution and Azure app portal registration with the .NET CLI `dotnet new` command and by manually creating the app registration in the Azure portal.</span></span>
>
> <span data-ttu-id="2b479-109">IDE가 업데이트되기 전에 Visual Studio를 사용하여 솔루션 및 Azure 앱 등록을 만들려면 **‘이 문서의 각 섹션’** 을 참조하고 Visual Studio가 솔루션을 만든 후에 앱의 구성 및 앱의 등록을 확인 또는 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="2b479-109">If you prefer to create the solution and Azure app registration with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the app's configuration and the app's registration after Visual Studio creates the solution.</span></span>

<span data-ttu-id="2b479-110">Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 AAD 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="2b479-111">앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD**).</span><span class="sxs-lookup"><span data-stu-id="2b479-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="2b479-112">**지원되는 계정 유형** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="2b479-113">이 환경에서는 **이 조직 디렉터리의 계정만** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="2b479-114">**리디렉션 URI** 드롭다운은 **SPA(단일 페이지 애플리케이션)** 으로 설정하고 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2b479-115">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2b479-116">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2b479-117">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2b479-118">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="2b479-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2b479-119">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2b479-120">**사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="2b479-121">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-121">Select **Register**.</span></span>

<span data-ttu-id="2b479-122">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-122">Record the following information:</span></span>

* <span data-ttu-id="2b479-123">애플리케이션(클라이언트) ID(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="2b479-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="2b479-124">디렉터리(테넌트) ID(예: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="2b479-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="2b479-125">**인증** > **플랫폼 구성** > **SPA(단일 페이지 애플리케이션)** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="2b479-126">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2b479-127">**암시적 허용** 에서 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택 **해제** 상태로 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-127">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="2b479-128">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2b479-129">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2b479-130">Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 AAD 앱을 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="2b479-131">앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD**).</span><span class="sxs-lookup"><span data-stu-id="2b479-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="2b479-132">**지원되는 계정 유형** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="2b479-133">이 환경에서는 **이 조직 디렉터리의 계정만** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="2b479-134">**리디렉션 URI** 드롭다운은 **웹** 으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2b479-135">Kestrel에서 실행되는 앱의 기본 포트는 5001입니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2b479-136">앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2b479-137">IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2b479-138">이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요.</span><span class="sxs-lookup"><span data-stu-id="2b479-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2b479-139">이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2b479-140">**사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="2b479-141">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-141">Select **Register**.</span></span>

<span data-ttu-id="2b479-142">다음과 같은 정보를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-142">Record the following information:</span></span>

* <span data-ttu-id="2b479-143">애플리케이션(클라이언트) ID(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="2b479-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="2b479-144">디렉터리(테넌트) ID(예: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="2b479-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="2b479-145">**인증** > **플랫폼 구성** > **웹** 에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-145">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="2b479-146">`https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2b479-147">**암시적 허용** 에서는 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-147">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="2b479-148">이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2b479-149">**저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="2b479-150">빈 폴더에 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-150">Create the app in an empty folder.</span></span> <span data-ttu-id="2b479-151">다음 명령에서 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="2b479-152">자리표시자</span><span class="sxs-lookup"><span data-stu-id="2b479-152">Placeholder</span></span>   | <span data-ttu-id="2b479-153">Azure Portal 이름</span><span class="sxs-lookup"><span data-stu-id="2b479-153">Azure portal name</span></span>       | <span data-ttu-id="2b479-154">예제</span><span class="sxs-lookup"><span data-stu-id="2b479-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="2b479-155">애플리케이션(클라이언트) ID</span><span class="sxs-lookup"><span data-stu-id="2b479-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="2b479-156">디렉터리(테넌트) ID</span><span class="sxs-lookup"><span data-stu-id="2b479-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="2b479-157">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="2b479-158">Azure Portal에서 앱의 플랫폼 구성 **리디렉션 URI** 는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="2b479-159">앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="2b479-160">앞에서 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="2b479-161">앱을 만든 후에는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="2b479-162">AAD 사용자 계정을 사용하여 앱에 로그인하기.</span><span class="sxs-lookup"><span data-stu-id="2b479-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="2b479-163">Microsoft API에 대한 액세스 토큰 요청하기.</span><span class="sxs-lookup"><span data-stu-id="2b479-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="2b479-164">자세한 내용은 다음을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b479-164">For more information, see:</span></span>
  * [<span data-ttu-id="2b479-165">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="2b479-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="2b479-166">[빠른 시작: 웹 API를 공개하는 애플리케이션 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b479-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="2b479-167">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="2b479-167">Authentication package</span></span>

<span data-ttu-id="2b479-168">앱이 회사 또는 학교 계정을 사용하도록 만들어진 경우(`SingleOrg`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal))에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="2b479-169">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2b479-170">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="2b479-171">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="2b479-172">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="2b479-173">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="2b479-173">Authentication service support</span></span>

<span data-ttu-id="2b479-174">사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="2b479-175">이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2b479-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b479-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="2b479-177"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2b479-178">앱을 구성하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="2b479-179">구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="2b479-180">예:</span><span class="sxs-lookup"><span data-stu-id="2b479-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="2b479-181">액세스 토큰 범위</span><span class="sxs-lookup"><span data-stu-id="2b479-181">Access token scopes</span></span>

<span data-ttu-id="2b479-182">Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="2b479-183">로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 기본 액세스 토큰 범위에 해당 범위를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="2b479-184">`AdditionalScopesToConsent`를 사용하여 추가 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="2b479-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="2b479-185">자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2b479-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2b479-186">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="2b479-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2b479-187">나가는 요청에 토큰 연결</span><span class="sxs-lookup"><span data-stu-id="2b479-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="2b479-188">로그인 모드</span><span class="sxs-lookup"><span data-stu-id="2b479-188">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="2b479-189">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="2b479-189">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="2b479-190">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="2b479-190">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="2b479-191">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2b479-191">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="2b479-192">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2b479-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="2b479-193">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2b479-193">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="2b479-194">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="2b479-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2b479-195">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2b479-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="2b479-196">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="2b479-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="2b479-197">Microsoft ID 플랫폼 설명서</span><span class="sxs-lookup"><span data-stu-id="2b479-197">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
