---
title: ASP.NET Core의 Google 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 Google 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110133"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="d2bd2-103">ASP.NET Core의 Google 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="d2bd2-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="d2bd2-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d2bd2-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d2bd2-105">이 자습서에서는 사용자가 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 3.0 프로젝트를 사용 하 여 Google 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="d2bd2-106">Google API 콘솔 프로젝트 및 클라이언트 ID 만들기</span><span class="sxs-lookup"><span data-stu-id="d2bd2-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="d2bd2-107">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet 패키지를 앱에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-107">Add the [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet package to the app.</span></span>
* <span data-ttu-id="d2bd2-108">[Google Sign-In를 웹 앱에 통합](https://developers.google.com/identity/sign-in/web/sign-in) (google 설명서)의 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-108">Follow the guidance in [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) (Google documentation).</span></span>
* <span data-ttu-id="d2bd2-109">[Google 콘솔](https://console.developers.google.com/apis/credentials)의 **자격 증명** 페이지에서 **자격 증명 만들기**  >  **OAuth 클라이언트 ID** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-109">In the **Credentials** page of the [Google console](https://console.developers.google.com/apis/credentials), select **CREATE CREDENTIALS** > **OAuth client ID**.</span></span>
* <span data-ttu-id="d2bd2-110">**응용 프로그램 종류** 대화 상자에서 **웹 응용 프로그램** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-110">In the **Application type** dialog, select **Web application**.</span></span> <span data-ttu-id="d2bd2-111">앱의 **이름을** 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-111">Provide a **Name** for the app.</span></span>
* <span data-ttu-id="d2bd2-112">**권한 있는 리디렉션** uri 섹션에서 **uri 추가** 를 선택 하 여 리디렉션 uri를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-112">In the **Authorized redirect URIs** section, select **ADD URI** to set the redirect URI.</span></span> <span data-ttu-id="d2bd2-113">예제 리디렉션 URI:. `https://localhost:{PORT}/signin-google` 여기서 `{PORT}` 자리 표시자는 앱의 포트입니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-113">Example redirect URI: `https://localhost:{PORT}/signin-google`, where the `{PORT}` placeholder is the app's port.</span></span>
* <span data-ttu-id="d2bd2-114">**만들기** 단추를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-114">Select the **CREATE** button.</span></span>
* <span data-ttu-id="d2bd2-115">앱의 구성에서 사용할 **클라이언트 ID** 및 **클라이언트 암호** 를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-115">Save the **Client ID** and **Client Secret** for use in the app's configuration.</span></span>
* <span data-ttu-id="d2bd2-116">사이트를 배포할 때 다음 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-116">When deploying the site, either:</span></span>
  * <span data-ttu-id="d2bd2-117">**Google 콘솔** 의 응용 프로그램의 리디렉션 uri를 앱의 배포 된 리디렉션 uri로 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-117">Update the app's redirect URI in the **Google Console** to the app's deployed redirect URI.</span></span>
  * <span data-ttu-id="d2bd2-118">프로덕션 리디렉션 URI를 사용 하 여 프로덕션 앱에 대 한 google **콘솔** 에서 새 google API 등록을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-118">Create a new Google API registration in the **Google Console** for the production app with its production redirect URI.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="d2bd2-119">Google 클라이언트 ID 및 암호 저장</span><span class="sxs-lookup"><span data-stu-id="d2bd2-119">Store the Google client ID and secret</span></span>

<span data-ttu-id="d2bd2-120">[암호 관리자](xref:security/app-secrets)를 사용 하 여 GOOGLE 클라이언트 ID 및 비밀 값과 같은 중요 한 설정을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-120">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="d2bd2-121">이 샘플에서는 다음 단계를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-121">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="d2bd2-122">[비밀 저장소 사용](xref:security/app-secrets#enable-secret-storage)의 지침에 따라 비밀 저장소에 대 한 프로젝트를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-122">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="d2bd2-123">비밀 키 및를 사용 하 여 로컬 비밀 저장소에 중요 한 설정을 저장 합니다 `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` .</span><span class="sxs-lookup"><span data-stu-id="d2bd2-123">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="d2bd2-124">Api [콘솔](https://console.developers.google.com/apis/dashboard)에서 api 자격 증명과 사용 현황을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-124">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="d2bd2-125">Google 인증 구성</span><span class="sxs-lookup"><span data-stu-id="d2bd2-125">Configure Google authentication</span></span>

<span data-ttu-id="d2bd2-126">다음에 Google 서비스를 추가 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d2bd2-126">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="d2bd2-127">Google로 로그인</span><span class="sxs-lookup"><span data-stu-id="d2bd2-127">Sign in with Google</span></span>

* <span data-ttu-id="d2bd2-128">앱을 실행 하 고 **로그인** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-128">Run the app and click **Log in**.</span></span> <span data-ttu-id="d2bd2-129">Google을 사용 하 여 로그인 할 수 있는 옵션이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-129">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="d2bd2-130">Google 단추를 **클릭 합니다 .이 단추는** 인증을 위해 google으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-130">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="d2bd2-131">Google 자격 증명을 입력 하면 웹 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-131">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="d2bd2-132"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Google 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-132">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="d2bd2-133">사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-133">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="d2bd2-134">기본 콜백 URI 변경</span><span class="sxs-lookup"><span data-stu-id="d2bd2-134">Change the default callback URI</span></span>

<span data-ttu-id="d2bd2-135">URI 세그먼트는 `/signin-google` Google 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-135">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="d2bd2-136">클래스의 상속 된 속성을 통해 Google 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다 <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> .</span><span class="sxs-lookup"><span data-stu-id="d2bd2-136">You can change the default callback URI while configuring the Google authentication middleware via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType>) property of the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d2bd2-137">문제 해결</span><span class="sxs-lookup"><span data-stu-id="d2bd2-137">Troubleshooting</span></span>

* <span data-ttu-id="d2bd2-138">로그인이 작동 하지 않고 오류가 발생 하지 않는 경우 개발 모드로 전환 하 여 문제를 더 쉽게 디버깅할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-138">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="d2bd2-139">Identity에서를 호출 하 여를 구성 하지 않은 경우 `services.AddIdentity` `ConfigureServices` ArgumentException에서 결과를 인증 하려고 하면 *' SignInScheme ' 옵션을 제공 해야* 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-139">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="d2bd2-140">이 자습서에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-140">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="d2bd2-141">초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 *요청 오류를 처리 하는 동안 데이터베이스 작업이 실패 했습니다* .</span><span class="sxs-lookup"><span data-stu-id="d2bd2-141">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="d2bd2-142">**마이그레이션 적용** 을 선택 하 여 데이터베이스를 만들고 페이지를 새로 고쳐 오류를 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-142">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d2bd2-143">다음 단계</span><span class="sxs-lookup"><span data-stu-id="d2bd2-143">Next steps</span></span>

* <span data-ttu-id="d2bd2-144">이 문서에서는 Google을 사용 하 여 인증할 수 있는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-144">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="d2bd2-145">[위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="d2bd2-146">Azure에 앱을 게시 한 후에는 `ClientSecret` GOOGLE API 콘솔에서를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-146">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="d2bd2-147">`Authentication:Google:ClientId` `Authentication:Google:ClientSecret` Azure Portal에서 및을 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-147">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="d2bd2-148">구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d2bd2-148">The configuration system is set up to read keys from environment variables.</span></span>
