---
title: Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호
author: guardrex
description: Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱을 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: dd32db8eaac70cfb3dfb3872c43c28aed6a87657
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280904"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 ASP.NET Core Blazor WebAssembly 독립 실행형 앱 보호

이 문서에서는 인증을 위해 [Azure Active Directory(AAD) B2C](/azure/active-directory-b2c/overview)를 사용하는 [독립 실행형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)을 만드는 방법을 다룹니다.

[AAD B2C 테넌트 만들기(Azure 설명서)](/azure/active-directory-b2c/tutorial-create-tenant) 문서에 따라 테넌트를 만들거나 Azure Portal에서 앱이 사용할 기존 B2C 테넌트를 식별하세요. 사용할 테넌트를 만들거나 식별한 직후 이 문서로 돌아옵니다.

다음과 같은 정보를 기록해 둡니다.

* AAD B2C 인스턴스(예: 후행 슬래시를 포함하는 `https://contoso.b2clogin.com/`): 이 인스턴스는 Azure B2C 앱 등록의 스키마 및 호스트로, Azure Portal의 **앱 등록** 페이지에서 **엔드포인트** 창을 열어 찾을 수 있습니다.
* AAD B2C 주/게시자/테넌트 도메인(예: `contoso.onmicrosoft.com`): 도메인은 Azure Portal에서 등록된 앱의 **브랜딩** 블레이드에 **게시자 도메인** 으로 표시되어 있습니다.

AAD B2C 앱을 등록합니다.

::: moniker range=">= aspnetcore-5.0"

1. **Azure Active Directory** > **앱 등록** 에서 **새 등록** 을 선택합니다.
1. 앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD B2C**).
1. **지원되는 계정 유형** 으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증** 을 선택합니다.
1. **리디렉션 URI** 드롭다운은 **SPA(단일 페이지 애플리케이션)** 으로 설정하고 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다. Kestrel에서 실행되는 앱의 기본 포트는 5001입니다. 앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다. IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다. 이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요. 이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.
1. **권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 이 선택되었는지 확인합니다.
1. **등록** 을 선택합니다.

애플리케이션(클라이언트) ID를 기록해 둡니다(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`).

**인증** > **플랫폼 구성** > **SPA(단일 페이지 애플리케이션)** 에서 다음을 수행합니다.

1. `https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.
1. **암시적 허용** 에서 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택 **해제** 상태로 유지합니다.
1. 이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.
1. **저장** 단추를 선택합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. **Azure Active Directory** > **앱 등록** 에서 **새 등록** 을 선택합니다.
1. 앱의 **이름** 을 지정합니다(예: **Blazor 독립 실행형 AAD B2C**).
1. **지원되는 계정 유형** 으로 다중 테넌트 옵션 **조직 디렉터리 또는 ID 공급자의 계정. Azure AD B2C를 사용하여 사용자 인증** 을 선택합니다.
1. **리디렉션 URI** 드롭다운은 **웹** 으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다. Kestrel에서 실행되는 앱의 기본 포트는 5001입니다. 앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다. IIS Express의 경우, 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 앱 속성에서 확인할 수 있습니다. 이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요. 이 항목의 뒷부분에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.
1. **권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 이 선택되었는지 확인합니다.
1. **등록** 을 선택합니다.

애플리케이션(클라이언트) ID를 기록해 둡니다(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`).

**인증** > **플랫폼 구성** > **웹** 에서 다음을 수행합니다.

1. `https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인합니다.
1. **암시적 허용** 에서는 **액세스 토큰** 및 **ID 토큰** 의 확인란을 선택합니다.
1. 이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.
1. **저장** 단추를 선택합니다.

::: moniker-end

**홈** > **Azure AD B2C** > **사용자 흐름** 에서:

[가입 및 로그인 사용자 흐름 만들기](/azure/active-directory-b2c/tutorial-create-user-flows)

최소한 **애플리케이션 클레임** > **표시 이름** 사용자 특성을 선택하여 `LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)의 `context.User.Identity.Name`을 채웁니다.

앱에 대해 만들어진 가입 및 로그인 사용자 흐름 이름을 기록해 둡니다(예: `B2C_1_signupsignin`).

빈 폴더에서 다음 명령의 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| 자리표시자                   | Azure Portal 이름               | 예제                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | 인스턴스                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | 애플리케이션(클라이언트) ID         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | 가입/로그인 사용자 흐름       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | 주/게시자/테넌트 도메인 | `contoso.onmicrosoft.com`              |

`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.

> [!NOTE]
> Azure Portal에서 앱의 플랫폼 구성 **리디렉션 URI** 는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.
>
> 앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 앱 속성에서 확인할 수 있습니다.
>
> 앞에서 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

앱을 만든 후에는 다음을 수행할 수 있습니다.

* AAD 사용자 계정을 사용하여 앱에 로그인하기.
* Microsoft API에 대한 액세스 토큰 요청하기. 자세한 내용은 다음을 참조하세요.
  * [액세스 토큰 범위](#access-token-scopes)
  * [빠른 시작: 웹 API를 공개하는 애플리케이션 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조하세요.

## <a name="authentication-package"></a>인증 패키지

앱이 개별 B2C 계정을 사용하도록 만들어진 경우(`IndividualB2C`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal))에 대한 패키지 참조를 받습니다. 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.

앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)의 패키지 **버전 기록** 에서 찾을 수 있습니다.

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지를 앱에 추가합니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다. 이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다. 앱을 구성하는 데 필요한 값은 앱을 등록할 때 AAD 구성에서 얻을 수 있습니다.

구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

예:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a>액세스 토큰 범위

Blazor WebAssembly 템플릿은 앱이 보안 API에 대한 액세스 토큰을 요청하도록 자동으로 구성하지 않습니다. 로그인 흐름의 일부로 액세스 토큰을 프로비저닝하려면 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>의 기본 액세스 토큰 범위에 해당 범위를 추가해야 합니다.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

`AdditionalScopesToConsent`를 사용하여 추가 범위를 지정합니다.

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.

* [추가 액세스 토큰 요청](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [나가는 요청에 토큰 연결](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>로그인 모드

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Imports 파일

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a>App 구성 요소

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a>Authentication 구성 요소

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/security/webassembly/additional-scenarios>
* [Authentication.MSAL JavaScript 라이브러리의 사용자 지정 버전 빌드](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [자습서: Azure Active Directory B2C 테넌트 만들기](/azure/active-directory-b2c/tutorial-create-tenant)
* [자습서: Azure Active Directory B2C에서 애플리케이션 등록](/azure/active-directory-b2c/tutorial-register-applications)
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
