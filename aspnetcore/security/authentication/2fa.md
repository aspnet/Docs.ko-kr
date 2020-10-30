---
title: ASP.NET Core SMS를 사용 하는 2 단계 인증
author: rick-anderson
description: ASP.NET Core 앱을 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 알아봅니다.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/2fa
ms.openlocfilehash: 1ee9e656c2e631c9b5588149e0a75e07108baff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051267"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="d1c95-103">ASP.NET Core SMS를 사용 하는 2 단계 인증</span><span class="sxs-lookup"><span data-stu-id="d1c95-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="d1c95-104">[Rick Anderson](https://twitter.com/RickAndMSFT) 및 [스위스-개발자](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="d1c95-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="d1c95-105">시간 기반 일회용 암호 알고리즘 (TOTP)을 사용 하는 2 단계 인증 (2FA) authenticator 앱은 2FA의 업계 권장 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="d1c95-106">2FA는 TOTP를 사용 하 여 SMS 2FA에 선호 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="d1c95-107">자세한 내용은 ASP.NET Core ASP.NET Core 2.0 이상에 대해 [TOTP authenticator 앱에 대 한 QR 코드 생성 사용](xref:security/authentication/identity-enable-qrcodes) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d1c95-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="d1c95-108">이 자습서에서는 SMS를 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="d1c95-109">지침은 [twilio](https://www.twilio.com/) 및 관련 [sms](https://www.aspsms.com/asp.net/identity/core/testcredits/)에 제공 되지만 다른 sms 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="d1c95-110">이 자습서를 시작 하기 전에 [계정 확인 및 암호 복구](xref:security/authentication/accconfirm) 를 완료 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="d1c95-111">[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)</span><span class="sxs-lookup"><span data-stu-id="d1c95-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="d1c95-112">[다운로드 방법](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d1c95-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="d1c95-113">새 ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="d1c95-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="d1c95-114">`Web2FA`개별 사용자 계정을 사용 하 여 라는 새 ASP.NET Core 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="d1c95-115">의 지침에 따라 <xref:security/enforcing-ssl> HTTPS를 설정 및 요구 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="d1c95-116">SMS 계정 만들기</span><span class="sxs-lookup"><span data-stu-id="d1c95-116">Create an SMS account</span></span>

<span data-ttu-id="d1c95-117">SMS 계정 (예: [twilio](https://www.twilio.com/) 또는 기타 [sms](https://www.aspsms.com/asp.net/identity/core/testcredits/))을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="d1c95-118">인증 자격 증명을 기록 합니다 (twilio: accountSid 및 authToken,: Userkey 및 Password 용).</span><span class="sxs-lookup"><span data-stu-id="d1c95-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="d1c95-119">SMS 공급자 자격 증명 파악</span><span class="sxs-lookup"><span data-stu-id="d1c95-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="d1c95-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="d1c95-120">**Twilio:**</span></span>

<span data-ttu-id="d1c95-121">Twilio 계정의 대시보드 탭에서 **계정 SID** 및 **인증 토큰** 을 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token** .</span></span>

<span data-ttu-id="d1c95-122">**다음 SMS:**</span><span class="sxs-lookup"><span data-stu-id="d1c95-122">**ASPSMS:**</span></span>

<span data-ttu-id="d1c95-123">계정 설정에서 **Userkey** 로 이동 하 여 **암호** 와 함께 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-123">From your account settings, navigate to **Userkey** and copy it together with your **Password** .</span></span>

<span data-ttu-id="d1c95-124">이러한 값은 나중에 키 및의 비밀 manager 도구를 사용 하 여에 저장 합니다 `SMSAccountIdentification` `SMSAccountPassword` .</span><span class="sxs-lookup"><span data-stu-id="d1c95-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="d1c95-125">SenderID/송신자 지정</span><span class="sxs-lookup"><span data-stu-id="d1c95-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="d1c95-126">**Twilio:** 숫자 탭에서 Twilio **전화 번호** 를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number** .</span></span>

<span data-ttu-id="d1c95-127">**다음 sms:** 보낸 사람 잠금 해제 메뉴 내에서 하나 이상의 발신자의 잠금을 해제 하거나 영숫자 송신자 (모든 네트워크에서 지원 되지 않음)를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="d1c95-128">나중에이 값을 키 내의 비밀 관리자 도구와 함께 저장 `SMSAccountFrom` 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="d1c95-129">SMS 서비스에 대 한 자격 증명 제공</span><span class="sxs-lookup"><span data-stu-id="d1c95-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="d1c95-130">[옵션 패턴](xref:fundamentals/configuration/options) 을 사용 하 여 사용자 계정 및 키 설정에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="d1c95-131">보안 SMS 키를 인출 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="d1c95-132">이 샘플의 경우 `SMSoptions` 클래스는 *Services/smsoptions .cs* 파일에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="d1c95-133">을 ( `SMSAccountIdentification` 를 `SMSAccountPassword` `SMSAccountFrom` [) 암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d1c95-134">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="d1c95-135">SMS 공급자에 대 한 NuGet 패키지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="d1c95-136">패키지 관리자 콘솔 (PMC)에서 다음을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="d1c95-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="d1c95-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="d1c95-138">**다음 SMS:**</span><span class="sxs-lookup"><span data-stu-id="d1c95-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="d1c95-139">*Service/MessageServices* 파일에 코드를 추가 하 여 SMS를 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="d1c95-140">Twilio 또는 다음 SMS 섹션을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="d1c95-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="d1c95-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="d1c95-142">**다음 SMS:**</span><span class="sxs-lookup"><span data-stu-id="d1c95-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="d1c95-143">사용할 시작 구성 `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="d1c95-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="d1c95-144">`SMSoptions` `ConfigureServices` *Startup.cs* 의 메서드에서 서비스 컨테이너에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs* :</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="d1c95-145">2단계 인증 사용</span><span class="sxs-lookup"><span data-stu-id="d1c95-145">Enable two-factor authentication</span></span>

<span data-ttu-id="d1c95-146">*Views/Manage/* :::no-loc(Razor)::: indexview 파일을 열고 주석 문자를 제거 합니다. 태그는 주석 처리 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-146">Open the *Views/Manage/Index.cshtml* :::no-loc(Razor)::: view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="d1c95-147">2 단계 인증을 사용 하 여 로그인</span><span class="sxs-lookup"><span data-stu-id="d1c95-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="d1c95-148">앱을 실행 하 고 새 사용자를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-148">Run the app and register a new user</span></span>

![Microsoft Edge에서 열린 웹 응용 프로그램 등록 보기](2fa/_static/login2fa1.png)

* <span data-ttu-id="d1c95-150">컨트롤러 관리에서 작업 메서드를 활성화 하는 사용자 이름을 누릅니다 `Index` .</span><span class="sxs-lookup"><span data-stu-id="d1c95-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="d1c95-151">전화 번호 **추가** 링크를 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-151">Then tap the phone number **Add** link.</span></span>

![뷰 관리-"추가" 링크를 누릅니다.](2fa/_static/login2fa2.png)

* <span data-ttu-id="d1c95-153">확인 코드를 수신 하는 전화 번호를 추가 하 고 **확인 코드 보내기** 를 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-153">Add a phone number that will receive the verification code, and tap **Send verification code** .</span></span>

![전화 번호 추가 페이지](2fa/_static/login2fa3.png)

* <span data-ttu-id="d1c95-155">확인 코드가 포함 된 문자 메시지를 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="d1c95-156">입력 하 고 **제출** 을 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-156">Enter it and tap **Submit**</span></span>

![전화 번호 확인 페이지](2fa/_static/login2fa4.png)

<span data-ttu-id="d1c95-158">문자 메시지를 얻지 못한 경우 twilio log 페이지를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="d1c95-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="d1c95-159">관리 보기에는 전화 번호가 성공적으로 추가 된 것이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-159">The Manage view shows your phone number was added successfully.</span></span>

![보기 관리-전화 번호를 추가 했습니다.](2fa/_static/login2fa5.png)

* <span data-ttu-id="d1c95-161">**사용** 을 탭 하 여 2 단계 인증을 사용 하도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-161">Tap **Enable** to enable two-factor authentication.</span></span>

![보기 관리-2 단계 인증 사용](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="d1c95-163">2 단계 인증 테스트</span><span class="sxs-lookup"><span data-stu-id="d1c95-163">Test two-factor authentication</span></span>

* <span data-ttu-id="d1c95-164">로그오프합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-164">Log off.</span></span>

* <span data-ttu-id="d1c95-165">로그인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-165">Log in.</span></span>

* <span data-ttu-id="d1c95-166">사용자 계정에서 2 단계 인증을 사용 하도록 설정 했으므로 두 번째 인증 단계를 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="d1c95-167">이 자습서에서는 전화 확인을 사용 하도록 설정 했습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="d1c95-168">기본 제공 템플릿을 사용 하 여 두 번째 단계로 전자 메일을 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="d1c95-169">QR 코드와 같은 인증을 위한 추가 두 번째 요소를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="d1c95-170">**제출** 을 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-170">Tap **Submit** .</span></span>

![확인 코드 보기 보내기](2fa/_static/login2fa7.png)

* <span data-ttu-id="d1c95-172">SMS 메시지에서 가져온 코드를 입력 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="d1c95-173">**이 브라우저 기억을** 확인란을 클릭 하면 동일한 장치 및 브라우저를 사용할 때 로그온 하는 데 2fa를 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="d1c95-174">2FA를 사용 하도록 설정 하 고 사용자가 장치에 액세스할 수 없는 경우 **이 브라우저** 를 사용 하도록 설정 하면 악의적인 사용자가 자신의 계정에 액세스 하는 데 사용 하는 강력한 2fa 보호 기능이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="d1c95-175">정기적으로 사용 하는 모든 개인 장치에서이 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="d1c95-176">**이 브라우저 기억을** 설정 하면 정기적으로 사용 하지 않는 장치에서 2fa의 보안을 강화 하 고 사용자의 장치에 대해 2fa를 거치지 않아도 편리 하 게 활용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-176">By setting  **Remember this browser** , you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![확인 보기](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="d1c95-178">무차별 암호 대입 공격 으로부터 보호 하기 위한 계정 잠금</span><span class="sxs-lookup"><span data-stu-id="d1c95-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="d1c95-179">계정 잠금은 2FA를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="d1c95-180">사용자가 로컬 계정이 나 소셜 계정을 통해 로그인 하면 2FA에서 실패 한 각 시도가 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="d1c95-181">실패 한 최대 액세스 시도 횟수에 도달 하면 사용자가 잠깁니다 (기본값: 5 분의 액세스 시도 실패 후 5 분 잠금).</span><span class="sxs-lookup"><span data-stu-id="d1c95-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="d1c95-182">성공적인 인증은 실패 한 액세스 시도 횟수를 다시 설정 하 고 시계를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="d1c95-183">최대 실패 한 액세스 시도 및 잠금 시간은 [Maxfailedaccessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)를 사용 하 여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="d1c95-184">다음은 실패 한 액세스 시도 10 분 후 10 분 동안 계정 잠금을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1c95-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="d1c95-185">[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) 가 `lockoutOnFailure` 다음을으로 설정 하는지 확인 합니다 `true` .</span><span class="sxs-lookup"><span data-stu-id="d1c95-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
