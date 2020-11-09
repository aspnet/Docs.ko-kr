---
title: ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정
author: rick-anderson
description: 이 샘플에서는 Microsoft 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3161e4f0f735294d69dd51634b424d1ed573e615
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060302"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="929f0-103">ASP.NET Core를 사용 하 여 Microsoft 계정 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="929f0-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="929f0-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="929f0-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="929f0-105">이 샘플에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 3.0 프로젝트를 사용 하 여 사용자가 회사, 학교 또는 개인 Microsoft 계정에 로그인 할 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="929f0-106">Microsoft 개발자 포털에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="929f0-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="929f0-107">프로젝트에 [AspNetCore. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="929f0-108">[Azure Portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동 하 고 Microsoft 계정를 만들거나 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="929f0-109">Microsoft 계정 없는 경우 **만들기** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-109">If you don't have a Microsoft account, select **Create one** .</span></span> <span data-ttu-id="929f0-110">로그인 하면 **앱 등록** 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="929f0-111">**새 등록** 선택</span><span class="sxs-lookup"><span data-stu-id="929f0-111">Select **New registration**</span></span>
* <span data-ttu-id="929f0-112">**이름** 을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-112">Enter a **Name** .</span></span>
* <span data-ttu-id="929f0-113">**지원 되는 계정 유형에** 대 한 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-113">Select an option for **Supported account types** .</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * <span data-ttu-id="929f0-114">이 `MicrosoftAccount` 패키지는 기본적으로 "모든 조직 디렉터리의 계정" 또는 "조직 디렉터리 및 Microsoft 계정의 계정" 옵션을 사용 하 여 만든 앱 등록을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-114">The `MicrosoftAccount` package supports App Registrations created using "Accounts in any organizational directory" or "Accounts in any organizational directory and Microsoft accounts" options by default.</span></span>
  * <span data-ttu-id="929f0-115">다른 옵션을 사용 하려면를 설정 하 `AuthorizationEndpoint` 고, `TokenEndpoint` `MicrosoftAccountOptions` 앱 등록의 **끝점** 페이지에 표시 되는 Url ( **개요** 페이지의 끝점을 클릭 하 여 사용 가능)에 Microsoft 계정 인증을 초기화 하는 데 사용 되는 멤버를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-115">To use other options, set `AuthorizationEndpoint` and `TokenEndpoint` members of `MicrosoftAccountOptions` used to initialize the Microsoft Account authentication to the URLs displayed on **Endpoints** page of the App Registration after it is created (available by clicking Endpoints on the **Overview** page).</span></span>
* <span data-ttu-id="929f0-116">**URI 리디렉션** 에서 추가 된로 개발 URL을 입력 `/signin-microsoft` 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-116">Under **Redirect URI** , enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="929f0-117">예: `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="929f0-117">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="929f0-118">이 샘플의 뒷부분에서 구성 된 Microsoft 인증 체계는 OAuth 흐름을 구현 하는 경로에서 요청을 자동으로 처리 합니다 `/signin-microsoft` .</span><span class="sxs-lookup"><span data-stu-id="929f0-118">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="929f0-119">**등록** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-119">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="929f0-120">클라이언트 암호 만들기</span><span class="sxs-lookup"><span data-stu-id="929f0-120">Create client secret</span></span>

* <span data-ttu-id="929f0-121">왼쪽 창에서 **인증서 및 암호** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-121">In the left pane, select **Certificates & secrets** .</span></span>
* <span data-ttu-id="929f0-122">**클라이언트 암호** 에서 **새 클라이언트 암호** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-122">Under **Client secrets** , select **New client secret**</span></span>

  * <span data-ttu-id="929f0-123">클라이언트 암호에 대 한 설명을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-123">Add a description for the client secret.</span></span>
  * <span data-ttu-id="929f0-124">**추가** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-124">Select the **Add** button.</span></span>

* <span data-ttu-id="929f0-125">**클라이언트 암호** 에서 클라이언트 암호의 값을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-125">Under **Client secrets** , copy the value of the client secret.</span></span>

<span data-ttu-id="929f0-126">URI 세그먼트가 `/signin-microsoft` Microsoft 인증 공급자의 기본 콜백으로 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-126">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="929f0-127">[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Microsoft 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-127">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="929f0-128">Microsoft 클라이언트 ID 및 암호 저장</span><span class="sxs-lookup"><span data-stu-id="929f0-128">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="929f0-129">기밀 [관리자](xref:security/app-secrets)를 사용 하 여 MICROSOFT 클라이언트 ID 및 비밀 값과 같은 중요 한 설정을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-129">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="929f0-130">이 샘플에서는 다음 단계를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-130">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="929f0-131">[비밀 저장소 사용](xref:security/app-secrets#enable-secret-storage)의 지침에 따라 비밀 저장소에 대 한 프로젝트를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-131">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="929f0-132">비밀 키 및를 사용 하 여 로컬 비밀 저장소에 중요 한 설정을 저장 합니다 `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` .</span><span class="sxs-lookup"><span data-stu-id="929f0-132">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="929f0-133">Microsoft 계정 인증 구성</span><span class="sxs-lookup"><span data-stu-id="929f0-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="929f0-134">Microsoft 계정 서비스를에 추가 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="929f0-134">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="929f0-135">Microsoft 계정 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="929f0-135">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="929f0-136">사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-136">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="929f0-137">Microsoft에 로그인 계정</span><span class="sxs-lookup"><span data-stu-id="929f0-137">Sign in with Microsoft Account</span></span>

<span data-ttu-id="929f0-138">앱을 실행 하 고 **로그인** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-138">Run the app and click **Log in** .</span></span> <span data-ttu-id="929f0-139">Microsoft에 로그인 할 수 있는 옵션이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-139">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="929f0-140">Microsoft를 클릭 하면 인증을 위해 Microsoft로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-140">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="929f0-141">Microsoft 계정을 사용 하 여 로그인 한 후에 앱에서 사용자의 정보에 액세스 하도록 허용 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-141">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="929f0-142">**예** 를 탭 하면 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-142">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="929f0-143">이제 Microsoft 자격 증명을 사용 하 여 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-143">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="929f0-144">문제 해결</span><span class="sxs-lookup"><span data-stu-id="929f0-144">Troubleshooting</span></span>

* <span data-ttu-id="929f0-145">Microsoft 계정 공급자가 로그인 오류 페이지로 리디렉션되는 경우 `#` Uri에서 (해시 태그) 바로 다음에 나오는 오류 제목 및 설명 쿼리 문자열 매개 변수를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-145">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="929f0-146">오류 메시지가 Microsoft 인증 문제를 나타내는 것 처럼 보이지만 가장 일반적인 원인은 **웹** 플랫폼에 지정 된 **리디렉션 uri** 와 일치 하지 않는 응용 프로그램 uri입니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-146">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="929f0-147">Identity에서를 호출 하 여가 구성 되지 않은 경우 `services.AddIdentity` `ConfigureServices` 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야* 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-147">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="929f0-148">이 샘플에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-148">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="929f0-149">초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리 하는 동안 데이터베이스 작업이 실패* 하 게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-149">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="929f0-150">**마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-150">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="929f0-151">다음 단계</span><span class="sxs-lookup"><span data-stu-id="929f0-151">Next steps</span></span>

* <span data-ttu-id="929f0-152">이 문서에서는 Microsoft를 사용 하 여 인증할 수 있는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-152">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="929f0-153">[위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-153">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="929f0-154">Azure 웹 앱에 웹 사이트를 게시 한 후에는 Microsoft 개발자 포털에서 새 클라이언트 암호를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-154">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="929f0-155">`Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` Azure Portal에서 및을 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-155">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="929f0-156">구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="929f0-156">The configuration system is set up to read keys from environment variables.</span></span>
