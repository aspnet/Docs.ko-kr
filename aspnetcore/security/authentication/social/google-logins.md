---
title: ASP.NET Core의 Google 외부 로그인 설정
author: rick-anderson
description: 이 자습서에서는 Google 계정 사용자 인증을 기존 ASP.NET Core 앱에 통합 하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
ms.openlocfilehash: 111ea7c972778dfd5296d0401c16563aeaa36a63
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060315"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core의 Google 외부 로그인 설정

작성자: [Valeriy Novytskyy](https://github.com/01binary) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

이 자습서에서는 사용자가 [이전 페이지](xref:security/authentication/social/index)에서 만든 ASP.NET Core 3.0 프로젝트를 사용 하 여 Google 계정으로 로그인 할 수 있도록 하는 방법을 보여 줍니다.

## <a name="create-a-google-api-console-project-and-client-id"></a>Google API 콘솔 프로젝트 및 클라이언트 ID 만들기

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)를 설치 합니다.
* [Google Sign-In를 웹 앱에 통합](https://developers.google.com/identity/sign-in/web/sign-in) 으로 이동 하 여 **프로젝트 구성** 을 선택 합니다.
* **OAuth 클라이언트 구성** 대화 상자에서 **웹 서버** 를 선택 합니다.
* **권한 있는 리디렉션 uri** 텍스트 항목 상자에서 리디렉션 uri를 설정 합니다. 예를 들어 `https://localhost:44312/signin-google`
* **클라이언트 ID** 및 **클라이언트 암호** 를 저장 합니다.
* 사이트를 배포할 때 **Google 콘솔** 에서 새 공용 url을 등록 합니다.

## <a name="store-the-google-client-id-and-secret"></a>Google 클라이언트 ID 및 암호 저장

[암호 관리자](xref:security/app-secrets)를 사용 하 여 GOOGLE 클라이언트 ID 및 비밀 값과 같은 중요 한 설정을 저장 합니다. 이 샘플에서는 다음 단계를 사용 합니다.

1. [비밀 저장소 사용](xref:security/app-secrets#enable-secret-storage)의 지침에 따라 비밀 저장소에 대 한 프로젝트를 초기화 합니다.
1. 비밀 키 및를 사용 하 여 로컬 비밀 저장소에 중요 한 설정을 저장 합니다 `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` .

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Api [콘솔](https://console.developers.google.com/apis/dashboard)에서 api 자격 증명과 사용 현황을 관리할 수 있습니다.

## <a name="configure-google-authentication"></a>Google 인증 구성

다음에 Google 서비스를 추가 합니다 `Startup.ConfigureServices` .

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Google로 로그인

* 앱을 실행 하 고 **로그인** 을 클릭 합니다. Google을 사용 하 여 로그인 할 수 있는 옵션이 나타납니다.
* Google 단추를 **클릭 합니다 .이 단추는** 인증을 위해 google으로 리디렉션됩니다.
* Google 자격 증명을 입력 하면 웹 사이트로 다시 리디렉션됩니다.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Google 인증에서 지 원하는 구성 옵션에 대 한 자세한 내용은 API 참조를 참조 하세요. 사용자에 대 한 다른 정보를 요청 하는 데 사용할 수 있습니다.

## <a name="change-the-default-callback-uri"></a>기본 콜백 URI 변경

URI 세그먼트는 `/signin-google` Google 인증 공급자의 기본 콜백으로 설정 됩니다. [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 클래스의 상속 된 [Remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) 속성을 통해 Google 인증 미들웨어를 구성 하는 동안 기본 콜백 URI를 변경할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

* 로그인이 작동 하지 않고 오류가 발생 하지 않는 경우 개발 모드로 전환 하 여 문제를 더 쉽게 디버깅할 수 있도록 합니다.
* Identity에서를 호출 하 여를 구성 하지 않은 경우 `services.AddIdentity` `ConfigureServices` ArgumentException에서 결과를 인증 하려고 하면 *' SignInScheme ' 옵션을 제공 해야* 합니다. 이 자습서에서 사용 되는 프로젝트 템플릿은이 작업이 수행 되도록 합니다.
* 초기 마이그레이션을 적용 하 여 사이트 데이터베이스를 만들지 않은 경우 *요청 오류를 처리 하는 동안 데이터베이스 작업이 실패 했습니다* . **마이그레이션 적용** 을 선택 하 여 데이터베이스를 만들고 페이지를 새로 고쳐 오류를 계속 합니다.

## <a name="next-steps"></a>다음 단계

* 이 문서에서는 Google을 사용 하 여 인증할 수 있는 방법을 살펴보았습니다. [위의 페이지](xref:security/authentication/social/index)에 나열 된 다른 공급자를 사용 하 여 인증 하는 유사한 방법을 따를 수 있습니다.
* Azure에 앱을 게시 한 후에는 `ClientSecret` GOOGLE API 콘솔에서를 다시 설정 합니다.
* `Authentication:Google:ClientId` `Authentication:Google:ClientSecret` Azure Portal에서 및을 응용 프로그램 설정으로 설정 합니다. 구성 시스템은 환경 변수에서 키를 읽도록 설정 되어 있습니다.
