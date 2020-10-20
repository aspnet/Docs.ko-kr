---
title: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호
author: guardrex
description: Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱을 보호하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/08/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e6f514793a2efde120f70ac58f4ad4be7516ada7
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900850"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Azure Active Directory를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호

작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)

이 문서에서는 인증을 위해 [AAD(Azure Active Directory )](https://azure.microsoft.com/services/active-directory/)를 사용하는 ‘[호스트형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)’을 만드는 방법을 설명합니다.

## <a name="register-apps-in-aad-and-create-solution"></a>AAD에 앱을 등록하고 솔루션 만들기

### <a name="create-a-tenant"></a>테넌트 만들기

[빠른 시작: 테넌트 설정](/azure/active-directory/develop/quickstart-create-new-tenant)의 지침에 따라 AAD에서 테넌트를 만듭니다.

### <a name="register-a-server-api-app"></a>서버 API 앱 등록

[빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목의 지침에 따라 ‘서버 API 앱’에 대해 AAD 앱을 등록하고 다음을 수행합니다.

1. **Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.
1. 앱의 **이름**을 지정합니다(예: **Blazor Server AAD**).
1. **지원되는 계정 유형**을 선택합니다. 이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.
1. 이 시나리오에서는 ‘서버 API 앱’에 **리디렉션 URI**가 필요하지 않으므로 드롭다운이 **웹**으로 설정된 상태로 두고 리디렉션 URI를 입력하지 않습니다.
1. **권한** > **openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란의 선택을 취소합니다.
1. **등록**을 선택합니다.

다음과 같은 정보를 기록해 둡니다.

* 서버 API 앱 애플리케이션(클라이언트) ID(예: `41451fa7-82d9-4673-8fa5-69eff5a761fd`)
* 디렉터리(테넌트) ID(예: `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)
* AAD 주/게시자/테넌트 도메인(예: `contoso.onmicrosoft.com`): 도메인은 Azure Portal에서 등록된 앱의 **브랜딩** 블레이드에 **게시자 도메인**으로 표시되어 있습니다.

앱에 로그인 또는 사용자 프로필 액세스가 필요하지 않으므로 **API 사용 권한**에서 **Microsoft Graph** > **User.Read** 사용 권한을 제거합니다.

**API 표시**에서:

1. **범위 추가**를 선택합니다.
1. **저장 및 계속**을 선택합니다.
1. **범위 이름**을 지정합니다(예: `API.Access`).
1. **관리자 동의 표시 이름**을 지정합니다(예: `Access API`).
1. **관리자 동의 설명**을 지정합니다(예: `Allows the app to access server app API endpoints.`).
1. **상태**가 **사용**으로 설정되었는지 확인합니다.
1. **범위 추가**를 선택합니다.

다음과 같은 정보를 기록해 둡니다.

* 앱 ID URI(예: `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` 또는 사용자가 제공하는 사용자 지정 값)
* 범위 이름(예: `API.Access`)

### <a name="register-a-client-app"></a>클라이언트 앱 등록

[빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록](/azure/active-directory/develop/quickstart-register-app) 및 후속 Azure AAD 항목의 지침에 따라 *`Client`* 앱에 대해 AAD 앱을 등록하고 다음을 수행합니다.

::: moniker range=">= aspnetcore-5.0"

1. **Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.
1. 앱의 **이름**을 지정합니다(예: **Blazor 클라이언트 AAD**).
1. **지원되는 계정 유형**을 선택합니다. 이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.
1. **리디렉션 URI** 드롭다운은 **SPA(단일 페이지 애플리케이션)** 으로 설정하고 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다. Kestrel에서 실행되는 앱의 기본 포트는 5001입니다. 앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다. IIS Express의 경우 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 *`Server`* 앱 속성에서 확인할 수 있습니다. 이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요. [앱 만들기](#create-the-app) 섹션에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.
1. **사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.
1. **등록**을 선택합니다.

*`Client`* 앱 애플리케이션(클라이언트) ID를 기록해 둡니다(예: `4369008b-21fa-427c-abaa-9b53bf58e538`).

**인증** > **플랫폼 구성** > **SPA(단일 페이지 애플리케이션)** 에서 다음을 수행합니다.

1. `https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI**가 있는지 확인합니다.
1. **암시적 허용**에서 **액세스 토큰** 및 **ID 토큰**의 확인란을 선택 **해제** 상태로 유지합니다.
1. 이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.
1. **저장** 단추를 선택합니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. **Azure Active Directory** > **앱 등록**에서 **새 등록**을 선택합니다.
1. 앱의 **이름**을 지정합니다(예: **Blazor 클라이언트 AAD**).
1. **지원되는 계정 유형**을 선택합니다. 이 환경에서는 **이 조직 디렉터리의 계정만**(단일 테넌트)을 선택합니다.
1. **리디렉션 URI** 드롭다운은 **웹**으로 설정된 상태로 두고, 리디렉션 URI를 `https://localhost:{PORT}/authentication/login-callback`으로 지정합니다. Kestrel에서 실행되는 앱의 기본 포트는 5001입니다. 앱이 다른 Kestrel 포트에서 실행되는 경우 해당 앱의 포트를 사용합니다. IIS Express의 경우 앱에 대해 임의로 생성되는 포트를 **디버그** 패널의 *`Server`* 앱 속성에서 확인할 수 있습니다. 이 시점에는 앱이 존재하지 않고 IIS Express 포트가 알려지지 않았으므로 앱이 만들어진 후에 이 단계로 돌아와서 리디렉션 URI를 업데이트하세요. [앱 만들기](#create-the-app) 섹션에서 IIS Express 사용자에게 리디렉션 URI를 업데이트하라고 알려 주는 설명이 표시됩니다.
1. **사용 권한**>**openid 및 offline_access 권한에 대한 관리자 동의 허용** 확인란을 선택 해제합니다.
1. **등록**을 선택합니다.

*`Client`* 앱 애플리케이션(클라이언트) ID를 기록해 둡니다(예: `4369008b-21fa-427c-abaa-9b53bf58e538`).

**인증** > **플랫폼 구성** > **웹**에서 다음을 수행합니다.

1. `https://localhost:{PORT}/authentication/login-callback`의 **리디렉션 URI**가 있는지 확인합니다.
1. **암시적 허용**에서는 **액세스 토큰** 및 **ID 토큰**의 확인란을 선택합니다.
1. 이 환경에서는 앱의 나머지 기본값을 그대로 사용해도 좋습니다.
1. **저장** 단추를 선택합니다.

::: moniker-end

**API 사용 권한**에서:

1. 앱에 **Microsoft Graph** > **User.Read** 사용 권한이 있는지 확인합니다.
1. **사용 권한 추가**를 선택하고 **내 API**를 선택합니다.
1. **이름** 열에서 ‘서버 API 앱’을 선택합니다(예: **Blazor Server AAD**).
1. **API** 목록을 엽니다.
1. API에 대한 액세스를 사용하도록 설정합니다(예: `API.Access`).
1. **권한 추가**를 선택합니다.
1. **{테넌트 이름}에 대한 관리자 동의 허용** 단추를 선택합니다. **예**를 선택하여 확인합니다.

### <a name="create-the-app"></a>앱 만들기

빈 폴더에서 다음 명령의 자리 표시자를 앞에서 기록해 둔 정보로 바꾸고 명령 셸에서 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| 자리표시자                  | Azure Portal 이름                                     | 예제                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`     | *`Client`* 앱의 애플리케이션(클라이언트) ID        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | 범위 이름                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | 서버 API 앱의 애플리케이션(클라이언트) ID      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | 애플리케이션 ID URI                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | 주/게시자/테넌트 도메인                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | 디렉터리(테넌트) ID                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 확인되지 않은 게시자 도메인에서 Azure 테넌트를 사용하는 경우 구성 변경이 필요할 수 있으며 이는 [앱 설정](#app-settings) 섹션에 설명되어 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> 확인되지 않은 게시자 도메인에서 Azure 테넌트를 사용하는 경우 구성 변경이 필요할 수 있으며 이는 [액세스 토큰 범위](#access-token-scopes) 섹션에 설명되어 있습니다.

::: moniker-end

> [!NOTE]
> Azure Portal에서 *`Client`* 앱의 플랫폼 구성 **리디렉션 URI**는 Kestrel 서버에서 기본 설정으로 실행되는 앱의 경우 포트 5001로 구성됩니다.
>
> *`Client`* 앱이 임의의 IIS Express 포트에서 실행되는 경우 앱의 포트는 **디버그** 패널의 ‘서버 API 앱’ 속성에서 확인할 수 있습니다.
>
> 앞에서 *`Client`* 앱의 알려진 포트로 포트가 구성되지 않았다면 Azure Portal에서 *`Client`* 앱의 등록으로 돌아가서 리디렉션 URI를 올바른 포트로 업데이트합니다.

## <a name="server-app-configuration"></a>*`Server`* 앱 구성

‘이 섹션은 솔루션의 **`Server`** 앱에 적용됩니다.’

### <a name="authentication-package"></a>인증 패키지

::: moniker range=">= aspnetcore-5.0"

Microsoft Identity 플랫폼을 통한 ASP.NET Core Web API에 대한 호출의 권한 부여 및 인증 지원은 다음 패키지에서 제공합니다.

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

자리 표시자 `{VERSION}`의 경우 앱의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 NuGet.org의 패키지 **버전 기록**에서 찾을 수 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

ASP.NET Core Web API에 대한 호출의 인증 및 권한 부여 지원은 [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) 패키지에서 제공합니다.

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)의 패키지 **버전 기록**에서 찾을 수 있습니다.

::: moniker-end

### <a name="authentication-service-support"></a>인증 서비스 지원

::: moniker range=">= aspnetcore-5.0"

`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정하고 JWT 전달자 처리기를 기본 인증 메서드로 구성합니다. <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> 메서드는 Microsoft Identity 플랫폼 v2.0을 사용하여 웹 API를 보호하도록 서비스를 구성합니다. 이 메서드에는 인증 옵션을 초기화하는 데 필요한 설정을 포함하는 앱 구성의 `AzureAd` 섹션이 필요합니다.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`AddAuthentication` 메서드는 앱 내에서 인증 서비스를 설정하고 JWT 전달자 처리기를 기본 인증 메서드로 구성합니다. <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> 메서드는 JWT 전달자 처리기에서 Azure Active Directory에서 내보낸 토큰의 유효성을 검사하는 데 필요한 특정 매개 변수를 설정합니다.

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 및 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>은 다음을 수행합니다.

* 앱이 수신 요청에 대해 토큰의 구문 분석 및 유효성 검사를 시도하도록 합니다.
* 올바른 자격 증명 없이 보호된 리소스에 액세스하려는 요청은 모두 실패하도록 합니다.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>User.Identity.Name

기본적으로 *`Server`* 앱 API는 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` 클레임 유형의 값을 사용하여 `User.Identity.Name`을 채웁니다(예: `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).

앱이 `name` 클레임 유형으로부터 값을 받도록 구성하려면 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>의 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>을 구성합니다.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>앱 설정

::: moniker range=">= aspnetcore-5.0"

`appsettings.json` 파일은 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 전달자 처리기를 구성하기 위한 옵션을 포함합니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

예:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`appsettings.json` 파일은 액세스 토큰의 유효성을 검사하는 데 사용되는 JWT 전달자 처리기를 구성하기 위한 옵션을 포함합니다.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

예:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a>WeatherForecast 컨트롤러

WeatherForecast 컨트롤러(*Controllers/WeatherForecastController.cs*)는 컨트롤러에 적용된 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 사용하여, 보호된 API를 노출합니다. 다음과 같은 사항을 이해하는 것이 **중요합니다**.

* 이 API 컨트롤러의 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 무단 액세스로부터 이 API 컨트롤러를 보호하는 유일한 항목입니다.
* Blazor WebAssembly 앱에서 사용되는 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성은 앱이 올바르게 작동하려면 사용자에게 권한이 부여되어야 한다는 힌트만 앱에 전달합니다.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>*`Client`* 앱 구성

‘이 섹션은 솔루션의 **`Client`** 앱에 적용됩니다.’

### <a name="authentication-package"></a>인증 패키지

앱이 회사 또는 학교 계정을 사용하도록 만들어진 경우(`SingleOrg`) 해당 앱은 자동으로 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview)([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal))에 대한 패키지 참조를 받습니다. 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.

앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)의 패키지 **버전 기록**에서 찾을 수 있습니다.

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지는 타동적으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지를 앱에 추가합니다.

### <a name="authentication-service-support"></a>인증 서비스 지원

서버 프로젝트로 요청을 전송할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스에 대한 지원이 추가됩니다.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.ServerAPI`).

사용자 인증에 대한 지원은 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 패키지에서 제공하는 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 확장 메서드를 통해 서비스 컨테이너에 등록됩니다. 이 메서드는 앱이 IP(Identity 공급자)와 상호 작용하는 데 필요한 서비스를 설정합니다.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 메서드는 콜백을 받아서 앱을 인증하는 데 필요한 매개 변수를 구성합니다. 앱을 구성하는 데 필요한 값은 앱을 등록할 때 Azure Portal AAD 구성에서 얻을 수 있습니다.

구성은 `wwwroot/appsettings.json` 파일에서 제공합니다.

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

예:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>액세스 토큰 범위

기본 액세스 토큰 범위는 다음과 같은 액세스 토큰 범위 목록을 나타냅니다.

* 기본적으로 로그인 요청에 포함되어 있음.
* 인증 직후에 액세스 토큰을 프로비저닝하는 데 사용됨.

모든 범위는 Azure Active Directory 규칙에 따라 동일한 앱에 속해 있어야 합니다. 필요에 따라 추가 API 앱에 대한 추가 범위가 추가될 수 있습니다.

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

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

자세한 내용은 ‘추가 시나리오’ 문서의 다음 섹션을 참조하세요.

* [추가 액세스 토큰 요청](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [나가는 요청에 토큰 연결](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>로그인 모드

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Imports 파일

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>App 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Authentication 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 구성 요소

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>앱 실행

서버 프로젝트에서 앱을 실행합니다. Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.

* 도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.
* **솔루션 탐색기**에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/security/webassembly/additional-scenarios>
* [보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft ID 플랫폼 설명서](/azure/active-directory/develop/)
