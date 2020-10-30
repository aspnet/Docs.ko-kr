---
title: ASP.NET Core SMS를 사용 하는 2 단계 인증
author: rick-anderson
description: ASP.NET Core 앱을 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 알아봅니다.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: 1ee9e656c2e631c9b5588149e0a75e07108baff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051267"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>ASP.NET Core SMS를 사용 하는 2 단계 인증

[Rick Anderson](https://twitter.com/RickAndMSFT) 및 [스위스-개발자](https://github.com/Swiss-Devs)

>[!WARNING]
> 시간 기반 일회용 암호 알고리즘 (TOTP)을 사용 하는 2 단계 인증 (2FA) authenticator 앱은 2FA의 업계 권장 방법입니다. 2FA는 TOTP를 사용 하 여 SMS 2FA에 선호 됩니다. 자세한 내용은 ASP.NET Core ASP.NET Core 2.0 이상에 대해 [TOTP authenticator 앱에 대 한 QR 코드 생성 사용](xref:security/authentication/identity-enable-qrcodes) 을 참조 하세요.

이 자습서에서는 SMS를 사용 하 여 2 단계 인증 (2FA)을 설정 하는 방법을 보여 줍니다. 지침은 [twilio](https://www.twilio.com/) 및 관련 [sms](https://www.aspsms.com/asp.net/identity/core/testcredits/)에 제공 되지만 다른 sms 공급자를 사용할 수 있습니다. 이 자습서를 시작 하기 전에 [계정 확인 및 암호 복구](xref:security/authentication/accconfirm) 를 완료 하는 것이 좋습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA) [다운로드 방법](xref:index#how-to-download-a-sample)

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET Core 프로젝트 만들기

`Web2FA`개별 사용자 계정을 사용 하 여 라는 새 ASP.NET Core 웹 앱을 만듭니다. 의 지침에 따라 <xref:security/enforcing-ssl> HTTPS를 설정 및 요구 합니다.

### <a name="create-an-sms-account"></a>SMS 계정 만들기

SMS 계정 (예: [twilio](https://www.twilio.com/) 또는 기타 [sms](https://www.aspsms.com/asp.net/identity/core/testcredits/))을 만듭니다. 인증 자격 증명을 기록 합니다 (twilio: accountSid 및 authToken,: Userkey 및 Password 용).

#### <a name="figuring-out-sms-provider-credentials"></a>SMS 공급자 자격 증명 파악

**Twilio**

Twilio 계정의 대시보드 탭에서 **계정 SID** 및 **인증 토큰** 을 복사 합니다.

**다음 SMS:**

계정 설정에서 **Userkey** 로 이동 하 여 **암호** 와 함께 복사 합니다.

이러한 값은 나중에 키 및의 비밀 manager 도구를 사용 하 여에 저장 합니다 `SMSAccountIdentification` `SMSAccountPassword` .

#### <a name="specifying-senderid--originator"></a>SenderID/송신자 지정

**Twilio:** 숫자 탭에서 Twilio **전화 번호** 를 복사 합니다.

**다음 sms:** 보낸 사람 잠금 해제 메뉴 내에서 하나 이상의 발신자의 잠금을 해제 하거나 영숫자 송신자 (모든 네트워크에서 지원 되지 않음)를 선택 합니다.

나중에이 값을 키 내의 비밀 관리자 도구와 함께 저장 `SMSAccountFrom` 합니다.

### <a name="provide-credentials-for-the-sms-service"></a>SMS 서비스에 대 한 자격 증명 제공

[옵션 패턴](xref:fundamentals/configuration/options) 을 사용 하 여 사용자 계정 및 키 설정에 액세스 합니다.

* 보안 SMS 키를 인출 하는 클래스를 만듭니다. 이 샘플의 경우 `SMSoptions` 클래스는 *Services/smsoptions .cs* 파일에서 만들어집니다.

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

을 ( `SMSAccountIdentification` 를 `SMSAccountPassword` `SMSAccountFrom` [) 암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 설정 합니다. 다음은 그 예입니다.

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* SMS 공급자에 대 한 NuGet 패키지를 추가 합니다. 패키지 관리자 콘솔 (PMC)에서 다음을 실행 합니다.

**Twilio**

`Install-Package Twilio`

**다음 SMS:**

`Install-Package ASPSMS`

* *Service/MessageServices* 파일에 코드를 추가 하 여 SMS를 사용 하도록 설정 합니다. Twilio 또는 다음 SMS 섹션을 사용 합니다.

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**다음 SMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>사용할 시작 구성 `SMSoptions`

`SMSoptions` `ConfigureServices` *Startup.cs* 의 메서드에서 서비스 컨테이너에를 추가 합니다.

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>2단계 인증 사용

*Views/Manage/* Razor indexview 파일을 열고 주석 문자를 제거 합니다. 태그는 주석 처리 되지 않습니다.

## <a name="log-in-with-two-factor-authentication"></a>2 단계 인증을 사용 하 여 로그인

* 앱을 실행 하 고 새 사용자를 등록 합니다.

![Microsoft Edge에서 열린 웹 응용 프로그램 등록 보기](2fa/_static/login2fa1.png)

* 컨트롤러 관리에서 작업 메서드를 활성화 하는 사용자 이름을 누릅니다 `Index` . 전화 번호 **추가** 링크를 탭 합니다.

![뷰 관리-"추가" 링크를 누릅니다.](2fa/_static/login2fa2.png)

* 확인 코드를 수신 하는 전화 번호를 추가 하 고 **확인 코드 보내기** 를 탭 합니다.

![전화 번호 추가 페이지](2fa/_static/login2fa3.png)

* 확인 코드가 포함 된 문자 메시지를 받게 됩니다. 입력 하 고 **제출** 을 탭 합니다.

![전화 번호 확인 페이지](2fa/_static/login2fa4.png)

문자 메시지를 얻지 못한 경우 twilio log 페이지를 참조 하세요.

* 관리 보기에는 전화 번호가 성공적으로 추가 된 것이 표시 됩니다.

![보기 관리-전화 번호를 추가 했습니다.](2fa/_static/login2fa5.png)

* **사용** 을 탭 하 여 2 단계 인증을 사용 하도록 설정 합니다.

![보기 관리-2 단계 인증 사용](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>2 단계 인증 테스트

* 로그오프합니다.

* 로그인합니다.

* 사용자 계정에서 2 단계 인증을 사용 하도록 설정 했으므로 두 번째 인증 단계를 제공 해야 합니다. 이 자습서에서는 전화 확인을 사용 하도록 설정 했습니다. 기본 제공 템플릿을 사용 하 여 두 번째 단계로 전자 메일을 설정할 수도 있습니다. QR 코드와 같은 인증을 위한 추가 두 번째 요소를 설정할 수 있습니다. **제출** 을 탭 합니다.

![확인 코드 보기 보내기](2fa/_static/login2fa7.png)

* SMS 메시지에서 가져온 코드를 입력 합니다.

* **이 브라우저 기억을** 확인란을 클릭 하면 동일한 장치 및 브라우저를 사용할 때 로그온 하는 데 2fa를 사용할 필요가 없습니다. 2FA를 사용 하도록 설정 하 고 사용자가 장치에 액세스할 수 없는 경우 **이 브라우저** 를 사용 하도록 설정 하면 악의적인 사용자가 자신의 계정에 액세스 하는 데 사용 하는 강력한 2fa 보호 기능이 제공 됩니다. 정기적으로 사용 하는 모든 개인 장치에서이 작업을 수행할 수 있습니다. **이 브라우저 기억을** 설정 하면 정기적으로 사용 하지 않는 장치에서 2fa의 보안을 강화 하 고 사용자의 장치에 대해 2fa를 거치지 않아도 편리 하 게 활용할 수 있습니다.

![확인 보기](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>무차별 암호 대입 공격 으로부터 보호 하기 위한 계정 잠금

계정 잠금은 2FA를 사용 하는 것이 좋습니다. 사용자가 로컬 계정이 나 소셜 계정을 통해 로그인 하면 2FA에서 실패 한 각 시도가 저장 됩니다. 실패 한 최대 액세스 시도 횟수에 도달 하면 사용자가 잠깁니다 (기본값: 5 분의 액세스 시도 실패 후 5 분 잠금). 성공적인 인증은 실패 한 액세스 시도 횟수를 다시 설정 하 고 시계를 다시 설정 합니다. 최대 실패 한 액세스 시도 및 잠금 시간은 [Maxfailedaccessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)를 사용 하 여 설정할 수 있습니다. 다음은 실패 한 액세스 시도 10 분 후 10 분 동안 계정 잠금을 구성 합니다.

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) 가 `lockoutOnFailure` 다음을으로 설정 하는지 확인 합니다 `true` .

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
