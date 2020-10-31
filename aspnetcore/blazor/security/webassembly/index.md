---
title: ASP.NET Core Blazor WebAssembly 보호
author: guardrex
description: Blazor WebAssemlby 앱을 SPA(단일 페이지 애플리케이션)로 보호하는 방법을 알아봅니다.
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: fc6fa075faa4fed1c2bf938d82c4dbfe631c31d3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055069"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core Blazor WebAssembly 보호

작성자: [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor WebAssembly 앱은 SPA(단일 페이지 애플리케이션)와 동일한 방식으로 보호됩니다. 사용자를 SPA에 인증하는 여러 가지 방법이 있지만, [OIDC(OpenID Connect)](https://openid.net/connect/)와 같은 [OAuth 2.0 프로토콜](https://oauth.net/) 기반의 구현을 사용하는 것이 가장 일반적이고 포괄적인 방법입니다.

## <a name="authentication-library"></a>인증 라이브러리

Blazor WebAssembly는 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리를 통해 OIDC를 사용한 앱 인증 및 권한 부여를 지원합니다. 이 라이브러리는 ASP.NET Core 백 엔드에 대해 원활하게 인증하기 위한 기본 형식 집합을 제공합니다. 이 라이브러리는 [Identity 서버](https://identityserver.io/) 위에 빌드된 API 권한 부여 지원과 ASP.NET Core Identity를 통합합니다. 이 라이브러리는 OP(OpenID 공급자)라고 하는 OIDC를 지원하는 타사 Identity 공급자(IP)에 대해 인증할 수 있습니다.

Blazor WebAssembly의 인증 지원은 기본 인증 프로토콜 세부 정보를 처리하는 데 사용되는 `oidc-client.js` 라이브러리를 기반으로 빌드됩니다.

SameSite cookie를 사용하는 것과 같이 SPA를 인증하기 위한 다른 옵션이 있습니다. 그러나 Blazor WebAssembly의 엔지니어링 설계에서는 OAuth 및 OIDC가 Blazor WebAssembly 앱의 인증을 위한 최고의 옵션으로 자리 잡았습니다. [JWT(JSON Web Token)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 기반의 [토큰 기반 인증](xref:security/anti-request-forgery#token-based-authentication)이 기능 및 보안상의 이유로 [cookie 기반 인증](xref:security/anti-request-forgery#cookie-based-authentication) 대신 선택되었습니다.

* 일부 요청에서는 토큰이 전송되지 않으므로 토큰 기반 프로토콜을 사용하면 공격 노출 영역이 적어집니다.
* 토큰은 명시적으로 전송되므로 [CSRF(교차 사이트 요청 위조)](xref:security/anti-request-forgery)로부터 서버 엔드포인트를 보호할 필요가 없습니다. 따라서 Blazor WebAssembly 앱을 MVC 또는 Razor Pages 앱과 함께 호스트할 수 있습니다.
* 토큰은 cookie보다 사용 권한이 더 좁습니다. 예를 들어 토큰을 사용하여 사용자 계정을 관리하거나 사용자 암호를 변경할 수 없습니다. 이러한 작업은 해당 기능이 명시적으로 구현된 경우에만 가능합니다.
* 토큰은 수명이 짧아서 기본적으로 1시간이므로 공격 기간이 제한됩니다. 또한 토큰은 언제든지 해지할 수 있습니다.
* 자체 포함 JWT는 클라이언트와 서버에 인증 프로세스를 보증합니다. 예를 들어 클라이언트에게는 수신한 토큰이 적합하고 주어진 인증 프로세스의 일부로 내보내 졌는지 검색하고 확인할 수단이 있습니다. 타사가 인증 프로세스 중에 토큰을 바꾸려고 하는 경우 클라이언트는 바뀐 토큰을 감지하고 사용하지 않을 수 있습니다.
* OAuth 및 OIDC를 사용하는 토큰은 사용자 에이전트가 제대로 동작하지 않아도 앱의 보안을 보장합니다.
* OAuth 및 OIDC와 같은 토큰 기반 프로토콜을 사용하면 호스트형 앱과 독립 실행형 앱을 동일한 보안 특성 집합을 사용하여 인증하고 권한 부여할 수 있습니다.

## <a name="authentication-process-with-oidc"></a>OIDC를 사용하는 인증 프로세스

[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 라이브러리는 OIDC를 사용하여 인증 및 권한 부여를 구현하는 여러 가지 기본 형식을 제공합니다. 대체로 인증은 다음과 같이 작동합니다.

* 익명 사용자가 로그인 단추를 선택하거나 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성이 적용된 페이지를 요청하면 앱의 로그인 페이지(`/authentication/login`)로 리디렉션됩니다.
* 로그인 페이지에서 인증 라이브러리는 권한 부여 엔드포인트로의 리디렉션을 준비합니다. 권한 부여 엔드포인트는 Blazor WebAssembly 앱 외부에 있으며 별도의 원본에 호스트될 수 있습니다. 엔드포인트는 사용자가 인증되었는지 여부를 확인하고 응답으로 하나 이상의 토큰을 발급합니다. 인증 라이브러리는 인증 응답을 받기 위한 로그인 콜백을 제공합니다.
  * 사용자가 인증되지 않은 경우 사용자는 기본 인증 시스템으로 리디렉션되며 이는 일반적으로 ASP.NET Core Identity입니다.
  * 사용자가 이미 인증된 경우에는 권한 부여 엔드포인트에서 적절한 토큰을 생성하고 브라우저를 다시 로그인 콜백 엔드포인트(`/authentication/login-callback`)로 리디렉션합니다.
* Blazor WebAssembly 앱이 로그인 콜백 엔드포인트(`/authentication/login-callback`)를 로드하면 인증 응답이 처리됩니다.
  * 인증 프로세스가 완료되면 사용자는 인증되고 필요에 따라 사용자가 요청한 원래의 보호된 URL로 다시 돌아갑니다.
  * 어떤 이유로든 인증 프로세스가 실패하는 경우 사용자는 로그인 실패 페이지(`/authentication/login-failed`)로 보내지고 오류가 표시됩니다.

## <a name="authentication-component"></a>`Authentication` 구성 요소

`Authentication` 구성 요소(`Pages/Authentication.razor`)는 원격 인증 작업을 처리하고 앱이 다음 작업을 수행하도록 허용합니다.

* 인증 상태에 대한 앱 경로를 구성합니다.
* 인증 상태에 대한 UI 콘텐츠를 설정합니다.
* 인증 상태를 관리합니다.

사용자 등록 또는 로그인 같은 인증 작업은 인증 작업 간에 상태를 유지하고 제어하는, Blazor 프레임워크의 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> 구성 요소에 전달됩니다.

자세한 내용과 예제를 보려면 <xref:blazor/security/webassembly/additional-scenarios>을 참조하십시오.

## <a name="authorization"></a>권한 부여

Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 권한 부여 확인을 무시할 수 있습니다. JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.

**항상 클라이언트 쪽 앱을 통해 액세스한 API 엔드포인트 내에서 서버의 권한 부여 확인을 수행합니다.**

## <a name="require-authorization-for-the-entire-app"></a>전체 앱에 대한 권한 부여 필요

다음 방법 중 하나를 사용하여 앱의 각 Razor 구성 요소에 [`[Authorize]` 특성](xref:blazor/security/index#authorize-attribute)([API 설명서](xref:System.Web.Mvc.AuthorizeAttribute))을 적용합니다.

* `_Imports.razor` 파일에 [`@attribute`](xref:mvc/views/razor#attribute) 지시문을 사용합니다.

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* `Pages` 폴더의 각 Razor 구성 요소에 특성을 추가합니다.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A>를 사용하는 정책에 대한 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> 설정은 지원되지 **않습니다** .

## <a name="refresh-tokens"></a>새로 고침 토큰

새로 고침 토큰은 Blazor WebAssembly 앱에서 클라이언트 쪽으로 보호할 수 없습니다. 따라서 직접 사용하기 위해 새로 고침 토큰이 앱에 전송되어서는 안됩니다.

새로 고침 토큰은 타사 API에 액세스하는 호스트된 Blazor WebAssembly 솔루션의 서버 쪽 앱에서 유지 관리하고 사용될 수 있습니다. 자세한 내용은 <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>를 참조하세요.

## <a name="establish-claims-for-users"></a>사용자 클레임 설정

앱에서는 종종 서버에 대한 웹 API 호출을 기반으로 사용자 클레임을 수행해야 합니다. 예를 들어 클레임은 앱에서 [권한 부여를 설정](xref:blazor/security/index#authorization)하는 데 자주 사용됩니다. 이러한 시나리오에서 앱은 서비스에 액세스하기 위해 액세스 토큰을 요청하고 이 토큰을 사용하여 클레임을 위한 사용자 데이터를 가져옵니다. 예제를 보려면 다음 리소스를 참조하세요.

* [추가 시나리오: 사용자의 사용자 지정](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="implementation-guidance"></a>구현 지침

이 개요의 문서에서는 Blazor WebAssembly 앱에서 특정 공급자에 대해 사용자를 인증하는 방법을 안내합니다.

독립 실행형 Blazor WebAssembly 앱:

* [OIDC 공급자 및 WebAssembly 인증 라이브러리에 대한 일반 지침](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [Microsoft 계정](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [AAD(Azure Active Directory)](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [AAD(Azure Active Directory) B2C](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

호스트된 Blazor WebAssembly 앱:

* [AAD(Azure Active Directory)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [AAD(Azure Active Directory) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [Identity서버](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

추가 구성 지침은 다음 문서에서 확인할 수 있습니다.

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

추가 구성 지침은 <xref:blazor/security/webassembly/additional-scenarios>를 참조하세요.

::: moniker-end
