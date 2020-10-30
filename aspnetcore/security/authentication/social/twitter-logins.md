---
title: ASP.NET Core로 Twitter 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 Twitter 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 47926d12ac5f922f2937df164d38ff6eb63cacf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053282"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core로 Twitter 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 샘플에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 3.0 프로젝트를 사용 하 여 사용자가 [Twitter 계정으로 로그인](https://dev.twitter.com/web/sign-in/desktop-browser) 할 수 있게 하는 방법을 보여 줍니다.

## <a name="create-the-app-in-twitter"></a>Twitter에서 앱 만들기

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet 패키지를 프로젝트에 추가 합니다.

* 으로 이동 하 여 [https://apps.twitter.com/](https://apps.twitter.com/) 로그인 합니다. Twitter 계정이 아직 없는 경우 **[지금 등록](https://twitter.com/signup)** 링크를 사용 하 여 계정을 만드세요.

* **앱 만들기** 를 선택 합니다. **앱 이름** , **응용 프로그램 설명** 및 공개 **웹 사이트** URI를 입력 합니다 (도메인 이름을 등록할 때까지 임시 일 수 있음).

* **Twitter로 로그인 사용** 옆의 확인란을 선택 합니다.

* AspNetCore.Identity 기본적으로 사용자에 게 전자 메일 주소가 있어야 합니다. **권한** 탭으로 이동 하 고 **편집** 단추를 클릭 한 다음 **사용자에 게 전자 메일 주소 요청** 옆의 확인란을 선택 합니다.

* `/signin-twitter` **콜백 url** 필드에를 추가 하 여 개발 URI를 입력 합니다 (예: `https://webapp128.azurewebsites.net/signin-twitter` ). 이 샘플의 뒷부분에서 구성 된 Twitter 인증 체계는 OAuth 흐름을 구현 하는 경로에서 요청을 자동으로 처리 합니다 `/signin-twitter` .

  > [!NOTE]
  > URI 세그먼트는 `/signin-twitter` Twitter 인증 공급자의 기본 콜백으로 설정 됩니다. [Twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Twitter 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.

* 양식의 나머지를 입력 하 고 **만들기** 를 선택 합니다. 새 응용 프로그램 세부 정보가 표시 됩니다.

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Twitter 소비자 API 키 및 암호 저장

[암호 관리자](xref:security/app-secrets)를 사용 하 여 TWITTER 소비자 API 키 및 암호와 같은 중요 한 설정을 저장 합니다. 이 샘플에서는 다음 단계를 사용 합니다.

1. [비밀 저장소 사용](xref:security/app-secrets#enable-secret-storage)의 지침에 따라 비밀 저장소에 대 한 프로젝트를 초기화 합니다.
1. 비밀 키 및를 사용 하 여 로컬 비밀 저장소에 중요 한 설정을 저장 합니다 `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` .

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

이러한 토큰은 새 Twitter 응용 프로그램을 만든 후에 **키 및 액세스 토큰** 탭에서 찾을 수 있습니다.

## <a name="configure-twitter-authentication"></a>Twitter 인증 구성

`ConfigureServices` *Startup.cs* 파일의 메서드에 Twitter 서비스를 추가 합니다.

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Twitter 인증에서 지원 되는 구성 옵션에 대 한 자세한 내용은 [Twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 참조를 참조 하세요. 사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.

## <a name="sign-in-with-twitter"></a>Twitter로 로그인

앱을 실행 하 고 **로그인** 을 선택 합니다. Twitter를 사용 하 여 로그인 하는 옵션이 표시 됩니다.

**Twitter** 로 리디렉션하여 인증을 위해 twitter를 클릭 합니다.

Twitter 자격 증명을 입력 하면 전자 메일을 설정할 수 있는 웹 사이트로 다시 리디렉션됩니다.

이제 Twitter 자격 증명을 사용 하 여 로그인 됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x에만 해당:** Identity 에서를 호출 하 여가 구성 되지 않은 경우 `services.AddIdentity` `ConfigureServices` 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야* 합니다. 이 샘플에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.
* 초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 요청 오류를 *처리 하는 동안 데이터베이스 작업이 실패* 하 게 됩니다. **마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Twitter를 사용 하 여 인증할 수 있는 방법을 살펴보았습니다. [위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.

* Azure 웹 앱에 웹 사이트를 게시 한 후에는 `ConsumerSecret` Twitter 개발자 포털에서를 다시 설정 해야 합니다.

* `Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` Azure Portal에서 및을 응용 프로그램 설정으로 설정 합니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.
