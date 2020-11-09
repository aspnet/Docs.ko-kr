---
title: ASP.NET Core에서 WS-Federation를 사용 하 여 사용자 인증
author: chlowell
description: 이 자습서에서는 ASP.NET Core 앱에서 WS-Federation를 사용 하는 방법을 보여 줍니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
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
uid: security/authentication/ws-federation
ms.openlocfilehash: ed78923a2bdd1ed683a72c0a6f34337a38350035
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053373"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="75217-103">ASP.NET Core에서 WS-Federation를 사용 하 여 사용자 인증</span><span class="sxs-lookup"><span data-stu-id="75217-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="75217-104">이 자습서에서는 사용자가 ADFS (Active Directory Federation Services) 또는 [Azure Active Directory](/azure/active-directory/) (AAD)와 같은 WS-Federation 인증 공급자를 사용 하 여 로그인 할 수 있도록 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="75217-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="75217-105">[Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)에 설명 된 ASP.NET Core 샘플 앱을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="75217-106">ASP.NET Core 앱의 경우 [AspNetCore WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)에서 WS-Federation 지원을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="75217-107">이 구성 요소는 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) 에서 이식 되며이 구성 요소의 여러 가지 메커니즘을 공유 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="75217-108">그러나 구성 요소에는 몇 가지 중요 한 차이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="75217-109">기본적으로 새 미들웨어는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-109">By default, the new middleware:</span></span>

* <span data-ttu-id="75217-110">임의로 로그인을 허용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="75217-111">WS-Federation 프로토콜의이 기능은 XSRF 공격에 취약 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="75217-112">그러나 옵션을 사용 하 여 사용 하도록 설정할 수 있습니다 `AllowUnsolicitedLogins` .</span><span class="sxs-lookup"><span data-stu-id="75217-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="75217-113">로그인 메시지의 모든 폼 게시를 확인 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="75217-114">`CallbackPath`로그인에 대 한 요청만 확인 됩니다. `CallbackPath` 기본값은로 설정 `/signin-wsfed` 되지만 상속 된 [remoteauthenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) 클래스의 callbackpath 속성을 통해 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="75217-115">이 경로는 [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) 옵션을 사용 하도록 설정 하 여 다른 인증 공급자와 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="75217-116">Active Directory에 앱 등록</span><span class="sxs-lookup"><span data-stu-id="75217-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="75217-117">Active Directory Federation Services</span><span class="sxs-lookup"><span data-stu-id="75217-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="75217-118">ADFS 관리 콘솔에서 서버의 **신뢰 당사자 트러스트 추가 마법사** 를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="75217-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![신뢰 당사자 트러스트 추가 마법사: 시작](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="75217-120">데이터를 수동으로 입력 하도록 선택:</span><span class="sxs-lookup"><span data-stu-id="75217-120">Choose to enter data manually:</span></span>

![신뢰 당사자 트러스트 추가 마법사: 데이터 원본 선택](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="75217-122">신뢰 당사자에 대 한 표시 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="75217-123">이 이름은 ASP.NET Core 앱에 중요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="75217-124">[AspNetCore. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 는 토큰 암호화를 지원 하지 않으므로 토큰 암호화 인증서를 구성 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![신뢰 당사자 트러스트 추가 마법사: 인증서 구성](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="75217-126">앱의 URL을 사용 하 여 WS-Federation 수동 프로토콜에 대 한 지원을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="75217-127">앱에 대 한 포트가 올바른지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-127">Verify the port is correct for the app:</span></span>

![신뢰 당사자 트러스트 추가 마법사: URL 구성](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="75217-129">이 URL은 HTTPS URL 이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="75217-130">IIS Express는 개발 중에 앱을 호스팅할 때 자체 서명 된 인증서를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="75217-131">Kestrel에는 수동 인증서 구성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="75217-132">자세한 내용은 [Kestrel 설명서](xref:fundamentals/servers/kestrel) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="75217-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="75217-133">마법사의 나머지 부분에서 **다음** 을 클릭 하 고 끝에를 **닫습니다** .</span><span class="sxs-lookup"><span data-stu-id="75217-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="75217-134">ASP.NET Core Identity**이름 ID** 클레임이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="75217-135">**클레임 규칙 편집** 대화 상자에서 하나를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![클레임 규칙 편집](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="75217-137">**변환 클레임 규칙 추가 마법사** 에서 기본 **LDAP 특성을 클레임으로 보내기** 템플릿을 선택 된 채로 두고 **다음** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-137">In the **Add Transform Claim Rule Wizard** , leave the default **Send LDAP Attributes as Claims** template selected, and click **Next** .</span></span> <span data-ttu-id="75217-138">**이름 ID** 발신 클레임에 **SAM-name** LDAP 특성을 매핑하는 규칙을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![변환 클레임 규칙 추가 마법사: 클레임 규칙 구성](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="75217-140">**Finish**  >  **클레임 규칙 편집** 창에서 **확인 마침을** 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="75217-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="75217-141">Azure Active Directory</span></span>

* <span data-ttu-id="75217-142">AAD 테 넌 트의 앱 등록 블레이드로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="75217-143">**새 응용 프로그램 등록** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-143">Click **New application registration** :</span></span>

![Azure Active Directory: 앱 등록](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="75217-145">앱 등록에 대 한 이름을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-145">Enter a name for the app registration.</span></span> <span data-ttu-id="75217-146">이는 ASP.NET Core 앱에는 중요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="75217-147">앱이 로그온 **url** 로 수신 하는 url을 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-147">Enter the URL the app listens on as the **Sign-on URL** :</span></span>

![Azure Active Directory: 앱 등록 만들기](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="75217-149">**끝점** 을 클릭 하 고 **페더레이션 메타 데이터 문서** URL을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="75217-150">WS-Federation 미들웨어는 `MetadataAddress` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: 끝점](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="75217-152">새 앱 등록으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-152">Navigate to the new app registration.</span></span> <span data-ttu-id="75217-153">**API 노출** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-153">Click **Expose an API** .</span></span> <span data-ttu-id="75217-154">응용 프로그램 ID URI **설정**  >  **저장** 을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-154">Click Application ID URI **Set** > **Save** .</span></span> <span data-ttu-id="75217-155">**응용 프로그램 ID URI** 를 적어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="75217-155">Make note of the  **Application ID URI** .</span></span> <span data-ttu-id="75217-156">WS-Federation 미들웨어는 `Wtrealm` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="75217-156">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: 앱 등록 속성](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a><span data-ttu-id="75217-158">없이 WS-Federation 사용 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="75217-158">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="75217-159">WS-Federation 미들웨어는 없이 사용할 수 있습니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="75217-159">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="75217-160">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="75217-160">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a><span data-ttu-id="75217-161">의 외부 로그인 공급자로 WS-Federation 추가 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="75217-161">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="75217-162">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) 에 대 한 종속성을 프로젝트에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-162">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="75217-163">다음에 WS-Federation 추가 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="75217-163">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="75217-164">WS-Federation를 사용 하 여 로그인</span><span class="sxs-lookup"><span data-stu-id="75217-164">Log in with WS-Federation</span></span>

<span data-ttu-id="75217-165">앱으로 이동 하 고 nav 헤더의 **로그인** 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="75217-165">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="75217-166">WsFederation: 로그인 페이지를 사용 하 여 로그인 할 수 있는 옵션이 있습니다. ![](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="75217-166">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="75217-167">Adfs를 공급자로 사용 하 여이 단추는 ![ adfs 로그인 페이지로 리디렉션됩니다.](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="75217-167">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="75217-168">Azure Active Directory 공급자 인 경우 단추는 aad 로그인 페이지로 리디렉션됩니다. ![ aad 로그인 페이지](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="75217-168">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="75217-169">새 사용자에 대 한 로그인이 성공 하면 앱의 사용자 등록 페이지로 리디렉션됩니다. ![ 페이지 등록](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="75217-169">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
