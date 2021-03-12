---
title: ASP.NET Core의 계정 확인 및 암호 복구
author: rick-anderson
description: 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: f71ae5e619b875c03401fa78320582c406875401
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586113"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>ASP.NET Core의 계정 확인 및 암호 복구

[Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)및 [Joe Audette](https://twitter.com/joeaudette)

이 자습서에서는 전자 메일 확인 및 암호 재설정을 사용 하 여 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다. 이 자습서는 시작 항목이 **아닙니다** . 다음에 대해 잘 알고 있어야 합니다.

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [인증](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>전제 조건

[.NET Core 3.0 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>인증을 사용 하 여 웹 앱 만들기 및 테스트

다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다. 등록 되 면 `/Identity/Account/RegisterConfirmation` 전자 메일 확인을 시뮬레이트하는 링크를 포함 하는 to 페이지로 리디렉션됩니다.

* 링크를 선택 `Click here to confirm your account` 합니다.
* **로그인** 링크를 선택 하 고 동일한 자격 증명을 사용 하 여 로그인 합니다.
* `Hello YourEmail@provider.com!`페이지로 리디렉션되는 링크를 선택 합니다 `/Identity/Account/Manage/PersonalData` .
* 왼쪽에서 **개인 데이터** 탭을 선택 하 고 **삭제** 를 선택 합니다.

### <a name="configure-an-email-provider"></a>전자 메일 공급자 구성

이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다. 전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다. 다른 전자 메일 공급자를 사용할 수 있습니다. SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다. SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.

SendGrid 계정에는 [발신자를 추가](https://sendgrid.com/docs/ui/sending-email/senders/)해야 할 수 있습니다.

보안 전자 메일 키를 인출 하는 클래스를 만듭니다. 이 샘플의 경우 *Services/AuthMessageSenderOptions* 을 만듭니다.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>SendGrid 사용자 비밀 구성

`SendGridUser` `SendGridKey` [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 및를 설정 합니다. 예를 들면 다음과 같습니다.

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Windows에서 Secret Manager는 디렉터리의 파일 *에 있는secrets.js* 의 키/값 쌍을 저장 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 합니다.

파일의 *secrets.js* 내용이 암호화 되지 않았습니다. 다음 태그는 파일 *의secrets.js* 을 보여 줍니다. `SendGridKey`값이 제거 되었습니다.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.

### <a name="install-sendgrid"></a>SendGrid 설치

이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.

NuGet 패키지를 설치 합니다 `SendGrid` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔에서 다음 명령을 입력 합니다.

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

콘솔에서 다음 명령을 입력 합니다.

```dotnetcli
dotnet add package SendGrid
```

---

무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.

### <a name="implement-iemailsender"></a>IEmailSender 구현

를 구현 하려면 `IEmailSender` 다음과 유사한 코드를 사용 하 여 *서비스/emailsender .cs* 를 만듭니다.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>전자 메일을 지원 하도록 시작 구성

`ConfigureServices` *Startup.cs* 파일의 메서드에 다음 코드를 추가 합니다.

* 을 `EmailSender` 임시 서비스로 추가 합니다.
* `AuthMessageSenderOptions`구성 인스턴스를 등록 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a>스 캐 폴드 RegisterConfirmation

[스 캐 폴드 Identity ](xref:security/authentication/scaffold-identity) 및 스 캐 폴드에 대 한 지침을 따릅니다 `RegisterConfirmation` .

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/main/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a>등록, 전자 메일 확인 및 암호 재설정

웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.

* 앱을 실행 하 고 새 사용자를 등록 합니다.
* 계정 확인 링크에 대 한 전자 메일을 확인 합니다. 전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.
* 링크를 클릭 하 여 전자 메일을 확인 합니다.
* 전자 메일 및 암호를 사용 하 여 로그인 합니다.
* 로그아웃합니다.

### <a name="test-password-reset"></a>암호 재설정 테스트

* 로그인 하는 경우 **로그 아웃** 을 선택 합니다.
* **로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.
* 계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.
* 암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다. 전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다. 암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.

<a name="resend"></a>

## <a name="resend-email-confirmation"></a>전자 메일 다시 보내기 확인

ASP.NET Core 5.0 이상에서는 **로그인** 페이지에서 **전자 메일 다시 보내기 확인** 링크를 선택 합니다.

### <a name="change-email-and-activity-timeout"></a>전자 메일 및 작업 시간 제한 변경

기본 비활성 시간 제한은 14 일입니다. 다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>모든 데이터 보호 토큰 변경 lifespans

다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

기본 제공 Identity 사용자 토큰 ( [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 참조)에는 [1 일 시간 제한이](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.

### <a name="change-the-email-token-lifespan"></a>전자 메일 토큰 수명 변경

[ Identity 사용자 토큰](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다. 이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.

사용자 지정 [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 및를 추가 합니다 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> .

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a>디버그 전자 메일

전자 메일을 사용할 수 없는 경우:

* `EmailSender.Execute`가 호출 되었는지 확인 하려면에서 중단점을 설정 `SendGridClient.SendEmailAsync` 합니다.
* 비슷한 코드를 사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다 `EmailSender.Execute` .
* [전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.
* 스팸 폴더를 확인 합니다.
* 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.
* 다른 전자 메일 계정으로 전송 해 보세요.

**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다. Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 SendGrid 비밀을 응용 프로그램 설정으로 설정 합니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.

## <a name="combine-social-and-local-login-accounts"></a>소셜 및 로컬 로그인 계정 결합

이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다. [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.

전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다. 다음 시퀀스에서는 " RickAndMSFT@gmail.com "이 (가) 로컬 로그인으로 먼저 생성 되지만 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.

![웹 응용 프로그램: RickAndMSFT@gmail.com 사용자 인증 됨](accconfirm/_static/rick.png)

**관리** 링크를 클릭 합니다. 이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.

![뷰 관리](accconfirm/_static/manage.png)

다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다. 다음 이미지에서 Facebook은 외부 인증 공급자입니다.

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

두 계정을 결합 했습니다. 두 계정을 사용 하 여 로그인 할 수 있습니다. 소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>사이트에 사용자가 있는 후 계정 확인 사용

사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다. 계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다. 기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.

* 기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.
* 기존 사용자를 확인 합니다. 예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>전제 조건

[.NET Core 2.2 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a>웹 앱 및 스 캐 폴드 만들기 Identity

다음 명령을 실행 하 여 인증을 사용 하는 웹 앱을 만듭니다.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.PasswordOptions>이에서 구성 된 경우 `Startup.ConfigureServices` 스 캐 폴드 pages의 속성에 대 한 [ `[StringLength]` 특성](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) 구성이 필요할 수 있습니다 `Password` Identity . `InputModel` `Password` 속성은 `Areas/Identity/Pages/Account/Register.cshtml.cs` 스 캐 폴딩 후 파일에 있습니다 Identity .

## <a name="test-new-user-registration"></a>새 사용자 등록 테스트

앱을 실행 하 고, **등록** 링크를 선택 하 고, 사용자를 등록 합니다. 이때 전자 메일에 대 한 유일한 유효성 검사는 특성을 사용 [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 합니다. 등록을 제출 하면 앱에 로그인 됩니다. 자습서의 뒷부분에서 새로운 사용자가 전자 메일의 유효성을 검사할 때까지 로그인 할 수 없도록 코드를 업데이트 합니다.

[!INCLUDE[](~/includes/view-identity-db.md)]

테이블의 필드는 `EmailConfirmed` `False` 입니다.

앱에서 확인 전자 메일을 보낼 때 다음 단계에서이 전자 메일을 다시 사용 하는 것이 좋습니다. 행을 마우스 오른쪽 단추로 클릭 하 고 **삭제** 를 선택 합니다. 전자 메일 별칭을 삭제 하면 다음 단계에서 보다 쉽게 수행할 수 있습니다.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>전자 메일 확인 필요

새 사용자 등록의 전자 메일을 확인 하는 것이 가장 좋습니다. 전자 메일 확인을 사용 하면 다른 사용자를 가장 하지 않는 것을 확인할 수 있습니다. 즉, 다른 사용자의 전자 메일을 사용 하 여 등록 하지 않았습니다. 토론 포럼이 있고 ""을 ""로 등록 하지 못하도록 하는 경우를 가정해 보겠습니다 yli@example.com nolivetto@contoso.com . 전자 메일 확인이 없으면 ""이 (가) nolivetto@contoso.com 앱에서 원치 않는 전자 메일을 받을 수 있습니다. ""로 실수로 등록 한 사용자가 ylo@example.com "yli"의 맞춤법 오류를 발견 했다고 가정 합니다. 앱에 올바른 전자 메일이 없기 때문에 암호 복구를 사용할 수 없습니다. 전자 메일 확인은 봇에서 제한 된 보호 기능을 제공 합니다. 전자 메일 확인은 전자 메일 계정이 많은 악의적인 사용자 로부터 보호를 제공 하지 않습니다.

일반적으로 새 사용자가 확인 된 전자 메일을 보내기 전에 데이터를 웹 사이트에 게시 하는 것을 방지 하려고 합니다.

`Startup.ConfigureServices`확인 된 전자 메일을 요구 하도록 업데이트 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` 등록 된 사용자가 전자 메일을 확인할 때까지 로그인 하지 못하도록 합니다.

### <a name="configure-email-provider"></a>전자 메일 공급자 구성

이 자습서에서는 [SendGrid](https://sendgrid.com) 를 사용 하 여 전자 메일을 보냅니다. 전자 메일을 보내려면 SendGrid 계정 및 키가 필요 합니다. 다른 전자 메일 공급자를 사용할 수 있습니다. ASP.NET Core 2.x에는 `System.Net.Mail` 앱에서 전자 메일을 보낼 수 있는가 포함 되어 있습니다. SendGrid 또는 다른 전자 메일 서비스를 사용 하 여 전자 메일을 보내는 것이 좋습니다. SMTP는 안전 하 게 보호 하 고 올바르게 설정 하기 어렵습니다.

보안 전자 메일 키를 인출 하는 클래스를 만듭니다. 이 샘플의 경우 *Services/AuthMessageSenderOptions* 을 만듭니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>SendGrid 사용자 비밀 구성

`SendGridUser` `SendGridKey` [암호 관리자 도구](xref:security/app-secrets)를 사용 하 여 및를 설정 합니다. 예를 들면 다음과 같습니다.

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Windows에서 Secret Manager는 디렉터리의 파일 *에 있는secrets.js* 의 키/값 쌍을 저장 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 합니다.

파일의 *secrets.js* 내용이 암호화 되지 않았습니다. 다음 태그는 파일 *의secrets.js* 을 보여 줍니다. `SendGridKey`값이 제거 되었습니다.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

자세한 내용은 [옵션 패턴](xref:fundamentals/configuration/options) 및 [구성](xref:fundamentals/configuration/index)을 참조 하세요.

### <a name="install-sendgrid"></a>SendGrid 설치

이 자습서에서는 [SendGrid](https://sendgrid.com/)를 통해 전자 메일 알림을 추가 하는 방법을 보여 주지만 SMTP 및 기타 메커니즘을 사용 하 여 전자 메일을 보낼 수 있습니다.

NuGet 패키지를 설치 합니다 `SendGrid` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔에서 다음 명령을 입력 합니다.

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

콘솔에서 다음 명령을 입력 합니다.

```dotnetcli
dotnet add package SendGrid
```

---

무료 SendGrid 계정에 등록 하려면 [무료로 SendGrid 시작](https://sendgrid.com/free/) 을 참조 하세요.

### <a name="implement-iemailsender"></a>IEmailSender 구현

를 구현 하려면 `IEmailSender` 다음과 유사한 코드를 사용 하 여 *서비스/emailsender .cs* 를 만듭니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>전자 메일을 지원 하도록 시작 구성

`ConfigureServices` *Startup.cs* 파일의 메서드에 다음 코드를 추가 합니다.

* 을 `EmailSender` 임시 서비스로 추가 합니다.
* `AuthMessageSenderOptions`구성 인스턴스를 등록 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>계정 확인 및 암호 복구 사용

템플릿에는 계정 확인 및 암호 복구를 위한 코드가 있습니다. `OnPostAsync` *영역/ Identity /Pages/Account/Register.cshtml.cs* 에서 메서드를 찾습니다.

다음 줄을 주석으로 처리 하 여 새로 등록 된 사용자가 자동으로 로그인 하지 않도록 합니다.

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

전체 메서드는 변경 된 줄이 강조 표시 된 상태로 표시 됩니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>등록, 전자 메일 확인 및 암호 재설정

웹 앱을 실행 하 고 계정 확인 및 암호 복구 흐름을 테스트 합니다.

* 앱을 실행 하 고 새 사용자를 등록 합니다.
* 계정 확인 링크에 대 한 전자 메일을 확인 합니다. 전자 메일을 받을 수 없는 경우 [디버그 전자 메일](#debug) 을 참조 하세요.
* 링크를 클릭 하 여 전자 메일을 확인 합니다.
* 전자 메일 및 암호를 사용 하 여 로그인 합니다.
* 로그아웃합니다.

### <a name="view-the-manage-page"></a>관리 페이지 보기

브라우저: ![ 사용자 이름을 사용 하 여 브라우저 창에서 사용자 이름을 선택 합니다.](accconfirm/_static/un.png)

관리 페이지는 **프로필** 탭이 선택 된 상태로 표시 됩니다. 전자 **메일** 에는 전자 메일이 확인 되었다는 확인란이 표시 됩니다.

### <a name="test-password-reset"></a>암호 재설정 테스트

* 로그인 하는 경우 **로그 아웃** 을 선택 합니다.
* **로그인** 링크를 선택 하 고 암호를 **잊으셨나요?** 링크를 선택 합니다.
* 계정을 등록 하는 데 사용한 전자 메일을 입력 합니다.
* 암호를 재설정 하는 링크가 포함 된 전자 메일이 전송 됩니다. 전자 메일을 확인 하 고 링크를 클릭 하 여 암호를 다시 설정 합니다. 암호를 성공적으로 재설정 한 후에는 전자 메일 및 새 암호로 로그인 할 수 있습니다.

## <a name="change-email-and-activity-timeout"></a>전자 메일 및 작업 시간 제한 변경

기본 비활성 시간 제한은 14 일입니다. 다음 코드는 비활성 시간 제한을 5 일로 설정 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>모든 데이터 보호 토큰 변경 lifespans

다음 코드는 모든 데이터 보호 토큰 제한 시간을 3 시간으로 변경 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

기본 제공 Identity 사용자 토큰 ( [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 참조)에는 [1 일 시간 제한이](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)있습니다.

### <a name="change-the-email-token-lifespan"></a>전자 메일 토큰 수명 변경

[ Identity 사용자 토큰](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) 의 기본 토큰 수명은 [1 일](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)입니다. 이 섹션에서는 전자 메일 토큰 수명을 변경 하는 방법을 보여 줍니다.

사용자 지정 [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) 및를 추가 합니다 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> .

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

서비스 컨테이너에 사용자 지정 공급자를 추가 합니다.

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>전자 메일 다시 보내기 확인

이 [GitHub 문제](https://github.com/dotnet/AspNetCore/issues/5410)를 참조하세요.

<a name="debug"></a>

### <a name="debug-email"></a>디버그 전자 메일

전자 메일을 사용할 수 없는 경우:

* `EmailSender.Execute`가 호출 되었는지 확인 하려면에서 중단점을 설정 `SendGridClient.SendEmailAsync` 합니다.
* 비슷한 코드를 사용 하 여 [전자 메일을 보내는 콘솔 앱](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 을 만듭니다 `EmailSender.Execute` .
* [전자 메일 작업](https://sendgrid.com/docs/User_Guide/email_activity.html) 페이지를 검토 합니다.
* 스팸 폴더를 확인 합니다.
* 다른 전자 메일 공급자 (Microsoft, Yahoo, Gmail 등)에서 다른 전자 메일 별칭을 사용해 보세요.
* 다른 전자 메일 계정으로 전송 해 보세요.

**보안 모범 사례** 는 테스트 및 개발에서 프로덕션 암호를 사용 **하지 않는** 것입니다. Azure에 앱을 게시 하는 경우 Azure 웹 앱 포털에서 응용 프로그램 설정으로 SendGrid 암호를 설정할 수 있습니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.

## <a name="combine-social-and-local-login-accounts"></a>소셜 및 로컬 로그인 계정 결합

이 섹션을 완료 하려면 먼저 외부 인증 공급자를 사용 하도록 설정 해야 합니다. [Facebook, Google 및 외부 공급자 인증](xref:security/authentication/social/index)을 참조 하세요.

전자 메일 링크를 클릭 하 여 로컬 및 소셜 계정을 결합할 수 있습니다. 다음 시퀀스에서는 " RickAndMSFT@gmail.com "이 (가) 로컬 로그인으로 먼저 생성 되지만 계정을 소셜 로그인으로 먼저 만든 다음 로컬 로그인을 추가할 수 있습니다.

![웹 응용 프로그램: RickAndMSFT@gmail.com 사용자 인증 됨](accconfirm/_static/rick.png)

**관리** 링크를 클릭 합니다. 이 계정과 연결 된 0 외부 (소셜 로그인)를 확인 합니다.

![뷰 관리](accconfirm/_static/manage.png)

다른 로그인 서비스에 대 한 링크를 클릭 하 고 앱 요청을 수락 합니다. 다음 이미지에서 Facebook은 외부 인증 공급자입니다.

![외부 로그인 보기 관리 Facebook 목록](accconfirm/_static/fb.png)

두 계정을 결합 했습니다. 두 계정을 사용 하 여 로그인 할 수 있습니다. 소셜 로그인 인증 서비스가 다운 되거나 소셜 계정에 대 한 액세스 권한이 손실 될 가능성이 있는 경우 사용자가 로컬 계정을 추가 하도록 할 수 있습니다.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>사이트에 사용자가 있는 후 계정 확인 사용

사용자가 있는 사이트에서 계정 확인을 사용 하도록 설정 하면 모든 기존 사용자가 잠깁니다. 계정이 확인 되지 않았기 때문에 기존 사용자가 잠깁니다. 기존 사용자 잠금을 해결 하려면 다음 방법 중 하나를 사용 합니다.

* 기존 사용자를 모두 확인 된 것으로 표시 하도록 데이터베이스를 업데이트 합니다.
* 기존 사용자를 확인 합니다. 예를 들어, 확인 링크가 포함 된 메일을 일괄 보냅니다.

::: moniker-end
