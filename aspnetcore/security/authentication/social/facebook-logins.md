---
title: ASP.NET Core의 Facebook 외부 로그인 설정
author: rick-anderson
description: 기존 ASP.NET Core 앱에 Facebook 계정 사용자 인증을 통합 하는 방법을 보여 주는 코드 예제를 보여 주는 자습서입니다.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053360"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core의 Facebook 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

이 자습서의 코드 예제에서는 [이전 페이지](xref:security/authentication/social/index)에서 만든 샘플 ASP.NET Core 3.0 프로젝트를 사용 하 여 사용자가 Facebook 계정으로 로그인 할 수 있도록 설정 하는 방법을 보여 줍니다. 먼저 [공식 단계](https://developers.facebook.com)를 수행 하 여 FACEBOOK 앱 ID를 만듭니다.

## <a name="create-the-app-in-facebook"></a>Facebook에서 앱 만들기

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 패키지를 프로젝트에 추가 합니다.

* [Facebook 개발자 앱](https://developers.facebook.com/apps/) 페이지로 이동 하 여 로그인 합니다. Facebook 계정이 아직 없는 경우 로그인 페이지에서 **facebook에 등록** 링크를 사용 하 여 하나를 만듭니다.  Facebook 계정이 있으면 지침에 따라 Facebook 개발자로 등록 합니다.

* **내 앱** 메뉴에서 **앱 만들기** 를 선택 하 여 새 앱 ID를 만듭니다.

   ![Microsoft Edge에서 오픈 개발자 용 Facebook 포털](index/_static/FBMyApps.png)

* 양식을 작성 하 고 **앱 ID 만들기** 단추를 탭 합니다.

  ![새 앱 ID 양식 만들기](index/_static/FBNewAppId.png)

* 새 앱 카드에서 **제품 추가** 를 선택 합니다.  **Facebook 로그인** 카드에서 **설정** 을 클릭 합니다. 

  ![제품 설정 페이지](index/_static/FBProductSetup.png)

* 첫 번째 페이지로 **플랫폼을 선택** 하 여 **빠른** 시작 마법사가 시작 됩니다. 왼쪽 아래에 있는 메뉴에서 **FaceBook 로그인** **설정** 링크를 클릭 하 여 이제 마법사를 무시 합니다.

  ![빠른 시작 건너뛰기](index/_static/FBSkipQuickStart.png)

* **클라이언트 OAuth 설정** 페이지가 표시 됩니다.

  ![클라이언트 OAuth 설정 페이지](index/_static/FBOAuthSetup.png)

* **유효한 OAuth 리디렉션 uri** 필드에 */signin-facebook* 를 추가 하 여 개발 URI를 입력 합니다 (예: `https://localhost:44320/signin-facebook` ). 이 자습서의 뒷부분에서 구성 된 Facebook 인증은 */signin-facebook* 경로에서 요청을 자동으로 처리 하 여 OAuth 흐름을 구현 합니다.

> [!NOTE]
> URI */signin-facebook* 는 facebook 인증 공급자의 기본 콜백으로 설정 됩니다. [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Facebook 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.

* **변경 내용 저장** 을 클릭합니다.

* 왼쪽 탐색 영역에서 **설정**  >  **기본** 링크를 클릭 합니다.

  이 페이지에서 및을 기록해 둡니다 `App ID` `App Secret` . 다음 섹션의 ASP.NET Core 응용 프로그램에 둘 다 추가 합니다.

* 사이트를 배포할 때 **Facebook 로그인** 설정 페이지를 다시 방문 하 여 새 공용 URI를 등록 해야 합니다.

## <a name="store-the-facebook-app-id-and-secret"></a>Facebook 앱 ID 및 암호 저장

[비밀 관리자](xref:security/app-secrets)를 사용 하 여 FACEBOOK 앱 ID 및 비밀 값과 같은 중요 한 설정을 저장 합니다. 이 샘플에서는 다음 단계를 사용 합니다.

1. [비밀 저장소 사용](xref:security/app-secrets#enable-secret-storage)의 지침에 따라 비밀 저장소에 대 한 프로젝트를 초기화 합니다.
1. 비밀 키 및를 사용 하 여 로컬 비밀 저장소에 중요 한 설정을 저장 합니다 `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` .

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Facebook 인증 구성

`ConfigureServices` *Startup.cs* 파일의 메서드에 Facebook 서비스를 추가 합니다.

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Facebook으로 로그인

* 앱을 실행 하 고 **로그인** 을 선택 합니다. 
* **다른 서비스를 사용 하 여 로그인** 에서 Facebook을 선택 합니다.
* 인증을 위해 **Facebook** 으로 리디렉션됩니다.
* Facebook 자격 증명을 입력 합니다.
* 전자 메일을 설정할 수 있는 사이트로 다시 리디렉션됩니다.

이제 Facebook 자격 증명을 사용 하 여 로그인 됩니다.

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>외부 로그인 권한 부여 취소에 대응

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> 사용자가 요청 된 권한 부여 요청을 승인 하지 않은 경우에서 사용자 에이전트에 대 한 리디렉션 경로를 제공할 수 있습니다.

다음 코드에서는 `AccessDeniedPath` 를로 설정 합니다 `"/AccessDeniedPathInfo"` .

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

`AccessDeniedPath`페이지에 다음 정보를 포함 하는 것이 좋습니다.

*  원격 인증을 취소 했습니다.
* 이 앱에는 인증이 필요 합니다.
* 다시 로그인을 시도 하려면 로그인 링크를 선택 합니다.

### <a name="test-accessdeniedpath"></a>테스트 AccessDeniedPath

* [Facebook.com](https://www.facebook.com/) 로 이동 합니다.
* 로그인 한 경우 로그 아웃 해야 합니다.
* 앱을 실행 하 고 Facebook 로그인을 선택 합니다.
* **지금 안 함** 을 선택 합니다. 지정 된 `AccessDeniedPath` 페이지로 리디렉션됩니다.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 참조를 참조 하세요. 구성 옵션을 사용 하 여 다음 작업을 수행할 수 있습니다.

* 사용자에 대 한 다른 정보를 요청 합니다.
* 쿼리 문자열 인수를 추가 하 여 로그인 환경을 사용자 지정 합니다.

## <a name="troubleshooting"></a>문제 해결

* **ASP.NET Core 2.x에만 해당:** Identity 에서를 호출 하 여가 구성 되지 않은 경우 `services.AddIdentity` `ConfigureServices` 인증을 시도 하면 ArgumentException이 발생 합니다. *' SignInScheme ' 옵션을 제공 해야* 합니다. 이 자습서에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.
* 초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 *요청 오류를 처리 하는 동안 데이터베이스 작업이 실패 했습니다* . **마이그레이션 적용** 을 탭 하 여 데이터베이스를 만들고 새로 고쳐 오류를 계속 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Facebook을 사용 하 여 인증 하는 방법을 살펴보았습니다. [위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.

* Azure 웹 앱에 웹 사이트를 게시 한 후에는 `AppSecret` Facebook 개발자 포털에서를 다시 설정 해야 합니다.

* `Authentication:Facebook:AppId` `Authentication:Facebook:AppSecret` Azure Portal에서 및을 응용 프로그램 설정으로 설정 합니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.
