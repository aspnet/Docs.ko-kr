---
title: ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증
author: rick-anderson
description: 이 자습서에는 외부 인증 공급자에 OAuth 2.0을 사용하여 ASP.NET Core 앱을 빌드하는 방법을 보여줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: ca5fd8f746e759d1994dde9a2a0d5b5fd6c88d1a
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870453"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>ASP.NET Core에서 Facebook, Google 및 외부 공급자 인증

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 사용자가 외부 인증 공급 기업의 자격 증명으로 OAuth 2.0을 사용하여 로그인할 수 있도록 ASP.NET Core 앱을 빌드하는 방법을 보여 줍니다.

[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) 및 [Microsoft](xref:security/authentication/microsoft-logins) 공급자는 다음 섹션에서 다루고 이 문서에서 만든 시작 프로젝트를 사용합니다. 다른 공급자는 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 및 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)와 같은 타사 패키지에서 사용할 수 있습니다.

사용자가 기존 자격 증명으로 로그인할 수 있도록 설정:

* 사용자에게 편리합니다.
* 로그인 프로세스를 관리하는 많은 복잡한 과정을 타사로 이전합니다.

소셜 로그인이 트래픽 및 고객 변환을 제공할 수 있는 방법에 대한 예제는 [Facebook](https://www.facebook.com/unsupportedbrowser) 및 [Twitter](https://dev.twitter.com/resources/case-studies)의 사례 연구를 참조하세요.

## <a name="create-a-new-aspnet-core-project"></a>새 ASP.NET Core 프로젝트 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 새 프로젝트를 만듭니다.
* **ASP.NET Core 웹 애플리케이션** 및 **다음** 을 선택합니다.
* **프로젝트 이름** 을 제공하고 **위치** 를 확인하거나 변경합니다. **만들기** 를 선택합니다.
* 드롭다운(**ASP.NET Core {x.y}** )에서 최신 버전의 ASP.NET Core를 선택한 다음 **웹 애플리케이션** 을 선택하세요.
* **인증** 에서 **변경** 을 선택하고 인증을 **개별 사용자 계정** 으로 설정합니다. **확인** 을 선택합니다.
* **새 ASP.NET Core 웹 애플리케이션 만들기** 창에서 **만들기** 를 선택합니다.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Mac용 Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* 터미널을 엽니다.  Visual Studio Code에서 [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 열 수 있습니다.

* 프로젝트를 포함할 폴더로 디렉터리를 변경(`cd`)합니다.

* Windows의 경우 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  macOS 및 Linux의 경우 다음 명령을 실행합니다.

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * `dotnet new` 명령은 *WebApp1* 폴더에서 새 Razor Pages 프로젝트를 만듭니다.
  * `-au Individual`은 개별 인증을 위한 코드를 만듭니다.
  * `-uld`에서는 Windows용 SQL Server Express의 경량 버전인 LocalDB를 사용합니다. SQLite를 사용하려면 `-uld`를 생략합니다.
  * `code` 명령은 Visual Studio Code의 새 인스턴스에서 *WebApp1* 폴더를 엽니다.

---

## <a name="apply-migrations"></a>마이그레이션 적용

* 앱을 실행하고 **레지스터** 링크를 선택합니다.
* 새 계정의 메일과 암호를 입력한 다음 **등록** 을 선택합니다.
* 지침에 따라 마이그레이션을 적용합니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>SecretManager를 사용하여 로그인 공급자에 의해 할당된 토큰 저장

소셜 로그인 공급자는 등록 프로세스 중에 **애플리케이션 ID** 및 **애플리케이션 암호** 토큰을 할당합니다. 정확한 토큰 이름은 공급자에 따라 달라집니다. 이러한 토큰은 앱이 API에 액세스하는 데 사용하는 자격 증명을 나타냅니다. 토큰은 [Secret Manager](xref:security/app-secrets#secret-manager)의 도움으로 앱 구성에 연결할 수 있는 "암호"를 구성합니다. Secret Manager는 *appsettings.json* 같은 구성 파일에 토큰을 저장하는 보다 안전한 대안입니다.

> [!IMPORTANT]
> Secret Manager는 개발 목적으로만 사용됩니다. [Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.

[ASP.NET Core에서 개발 중인 앱 암호의 안전한 스토리지](xref:security/app-secrets) 항목의 단계에 따라 아래의 각 로그인 공급자에 의해 할당된 토큰을 저장합니다.

## <a name="setup-login-providers-required-by-your-application"></a>애플리케이션에 필요한 로그인 공급자 설정

다음 항목을 사용하여 해당 공급자를 사용하도록 애플리케이션을 구성합니다.

* [Facebook](xref:security/authentication/facebook-logins) 지침
* [Twitter](xref:security/authentication/twitter-logins) 지침
* [Google](xref:security/authentication/google-logins) 지침
* [Microsoft](xref:security/authentication/microsoft-logins) 지침
* [다른 공급자](xref:security/authentication/otherlogins) 지침

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>필요에 따라 암호 설정

외부 로그인 공급자를 등록하는 경우 앱에 암호를 등록하지 않은 상태입니다. 그러면 사이트에 암호를 만들고 기억하지 않아도 되지만 외부 로그인 공급자에 따라 다르게 적용해야 합니다. 외부 로그인 공급자를 사용할 수 없는 경우 웹 사이트에 로그인할 수 없습니다.

외부 공급자로 로그인하는 프로세스 중에 설정한 전자 메일을 사용하여 암호를 만들고 로그인하려면:

* 오른쪽 위 모서리에 있는 **Hello &lt;이메일 별칭&gt;** 링크를 선택하여 **관리** 보기로 이동합니다.

![웹 애플리케이션 관리 뷰](index/_static/pass1a.png)

* **만들기** 를 선택합니다.

![암호 페이지 설정](index/_static/pass2a.png)

* 올바른 암호를 설정하고 사용자의 전자 메일을 사용하여 로그인하는 데 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 로그인 단추를 사용자 지정하는 방법에 대한 자세한 내용은 [이 GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/10563)를 참조하세요.
* 이 문서는 외부 인증을 고새하고 ASP.NET Core 앱에 외부 로그인을 추가하는 데 필요한 필수 구성 요소를 설명합니다.
* 앱에 필요한 공급자에 대한 로그인을 구성하기 위해 공급자 관련 페이지를 참조합니다.
* 사용자와 해당 액세스 및 새로 고침 토큰에 대한 추가 데이터를 유지할 수 있습니다. 자세한 내용은 <xref:security/authentication/social/additional-claims>를 참조하세요.
