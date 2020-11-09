---
title: ASP.NET Core의 Facebook 외부 로그인 설정
author: rick-anderson
description: 기존 ASP.NET Core 앱에 Facebook 계정 사용자 인증을 통합 하는 방법을 보여 주는 코드 예제를 보여 주는 자습서입니다.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053360"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="8d14f-103">ASP.NET Core의 Facebook 외부 로그인 설정</span><span class="sxs-lookup"><span data-stu-id="8d14f-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="8d14f-104">작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8d14f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="8d14f-105">이 자습서의 코드 예제에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 3.0 프로젝트를 사용 하 여 사용자가 Facebook 계정으로 로그인 할 수 있도록 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="8d14f-106">먼저 [공식 단계](https://developers.facebook.com)를 수행 하 여 FACEBOOK 앱 ID를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="8d14f-107">Facebook에서 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="8d14f-107">Create the app in Facebook</span></span>

* <span data-ttu-id="8d14f-108">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 패키지를 프로젝트에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="8d14f-109">[Facebook 개발자 앱](https://developers.facebook.com/apps/) 페이지로 이동 하 여 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="8d14f-110">Facebook 계정이 아직 없는 경우 로그인 페이지에서 **facebook에 등록** 링크를 사용 하 여 하나를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="8d14f-111">Facebook 계정이 있으면 지침에 따라 Facebook 개발자로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="8d14f-112">**내 앱** 메뉴에서 **앱 만들기** 를 선택 하 여 새 앱 ID를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Microsoft Edge에서 오픈 개발자 용 Facebook 포털](index/_static/FBMyApps.png)

* <span data-ttu-id="8d14f-114">양식을 작성 하 고 **앱 ID 만들기** 단추를 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* <span data-ttu-id="8d14f-116">새 앱 카드에서 **제품 추가** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-116">On the new App card, select **Add a Product** .</span></span>  <span data-ttu-id="8d14f-117">**Facebook 로그인** 카드에서 **설정** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![제품 설정 페이지](index/_static/FBProductSetup.png)

* <span data-ttu-id="8d14f-119">첫 번째 페이지로 **플랫폼을 선택** 하 여 **빠른** 시작 마법사가 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="8d14f-120">왼쪽 아래에 있는 메뉴에서 **FaceBook 로그인** **설정** 링크를 클릭 하 여 이제 마법사를 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![빠른 시작 건너뛰기](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="8d14f-122">**클라이언트 OAuth 설정** 페이지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="8d14f-124">**유효한 OAuth 리디렉션 uri** 필드에 */signin-facebook* 를 추가 하 여 개발 URI를 입력 합니다 (예: `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="8d14f-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="8d14f-125">이 자습서의 뒷부분에서 구성 된 Facebook 인증은 */signin-facebook* 경로에서 요청을 자동으로 처리 하 여 OAuth 흐름을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="8d14f-126">URI */signin-facebook* 는 facebook 인증 공급자의 기본 콜백으로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="8d14f-127">[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Facebook 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="8d14f-128">**변경 내용 저장** 을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-128">Click **Save Changes** .</span></span>

* <span data-ttu-id="8d14f-129">왼쪽 탐색 영역에서 **설정**  >  **기본** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="8d14f-130">이 페이지에서 및을 기록해 둡니다 `App ID` `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="8d14f-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="8d14f-131">다음 섹션의 ASP.NET Core 응용 프로그램에 둘 다 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="8d14f-132">사이트를 배포할 때 **Facebook 로그인** 설정 페이지를 다시 방문 하 여 새 공용 URI를 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="8d14f-133">Facebook 앱 ID 및 암호 저장</span><span class="sxs-lookup"><span data-stu-id="8d14f-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="8d14f-134">[비밀 관리자](xref:security/app-secrets)를 사용 하 여 FACEBOOK 앱 ID 및 비밀 값과 같은 중요 한 설정을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8d14f-135">이 샘플에서는 다음 단계를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="8d14f-136">[비밀 저장소 사용](xref:security/app-secrets#enable-secret-storage)의 지침에 따라 비밀 저장소에 대 한 프로젝트를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="8d14f-137">비밀 키 및를 사용 하 여 로컬 비밀 저장소에 중요 한 설정을 저장 합니다 `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="8d14f-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="8d14f-138">Facebook 인증 구성</span><span class="sxs-lookup"><span data-stu-id="8d14f-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="8d14f-139">`ConfigureServices` *Startup.cs* 파일의 메서드에 Facebook 서비스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="8d14f-140">Facebook으로 로그인</span><span class="sxs-lookup"><span data-stu-id="8d14f-140">Sign in with Facebook</span></span>

* <span data-ttu-id="8d14f-141">앱을 실행 하 고 **로그인** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-141">Run the app and select **Log in** .</span></span> 
* <span data-ttu-id="8d14f-142">**다른 서비스를 사용 하 여 로그인** 에서 Facebook을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-142">Under **Use another service to log in.** , select Facebook.</span></span>
* <span data-ttu-id="8d14f-143">인증을 위해 **Facebook** 으로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="8d14f-144">Facebook 자격 증명을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="8d14f-145">전자 메일을 설정할 수 있는 사이트로 다시 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="8d14f-146">이제 Facebook 자격 증명을 사용 하 여 로그인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="8d14f-147">외부 로그인 권한 부여 취소에 대응</span><span class="sxs-lookup"><span data-stu-id="8d14f-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="8d14f-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> 사용자가 요청 된 권한 부여 요청을 승인 하지 않은 경우에서 사용자 에이전트에 대 한 리디렉션 경로를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="8d14f-149">다음 코드에서는 `AccessDeniedPath` 를로 설정 합니다 `"/AccessDeniedPathInfo"` .</span><span class="sxs-lookup"><span data-stu-id="8d14f-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="8d14f-150">`AccessDeniedPath`페이지에 다음 정보를 포함 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="8d14f-151">원격 인증을 취소 했습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="8d14f-152">이 앱에는 인증이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-152">This app requires authentication.</span></span>
* <span data-ttu-id="8d14f-153">다시 로그인을 시도 하려면 로그인 링크를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="8d14f-154">테스트 AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="8d14f-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="8d14f-155">[Facebook.com](https://www.facebook.com/) 로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="8d14f-156">로그인 한 경우 로그 아웃 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="8d14f-157">앱을 실행 하 고 Facebook 로그인을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="8d14f-158">**지금 안 함** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-158">Select **Not now** .</span></span> <span data-ttu-id="8d14f-159">지정 된 `AccessDeniedPath` 페이지로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="8d14f-160">Facebook 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 참조를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="8d14f-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="8d14f-161">구성 옵션을 사용 하 여 다음 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="8d14f-162">사용자에 대 한 다른 정보를 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-162">Request different information about the user.</span></span>
* <span data-ttu-id="8d14f-163">쿼리 문자열 인수를 추가 하 여 로그인 환경을 사용자 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8d14f-164">문제 해결</span><span class="sxs-lookup"><span data-stu-id="8d14f-164">Troubleshooting</span></span>

* <span data-ttu-id="8d14f-165">**ASP.NET Core 2.x에만 해당:** Identity 에서를 호출 하 여가 구성 되지 않은 경우 `services.AddIdentity` `ConfigureServices` 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야* 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="8d14f-166">이 자습서에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="8d14f-167">초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 *요청 오류를 처리 하는 동안 데이터베이스 작업이 실패 했습니다* .</span><span class="sxs-lookup"><span data-stu-id="8d14f-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8d14f-168">**마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8d14f-169">다음 단계</span><span class="sxs-lookup"><span data-stu-id="8d14f-169">Next steps</span></span>

* <span data-ttu-id="8d14f-170">이 문서에서는 Facebook을 사용 하 여 인증 하는 방법을 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="8d14f-171">[위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="8d14f-172">Azure 웹 앱에 웹 사이트를 게시 한 후에는 `AppSecret` Facebook 개발자 포털에서를 다시 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="8d14f-173">`Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` Azure Portal에서 및을 응용 프로그램 설정으로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="8d14f-174">구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8d14f-174">The configuration system is set up to read keys from environment variables.</span></span>
