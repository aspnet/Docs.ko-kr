---
title: ASP.NET Core의 단일 페이지 앱에 대 한 인증 소개
author: javiercn
description: IdentityASP.NET Core 앱 내에서 호스팅되는 단일 페이지 앱과 함께 사용 합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051982"
---
# <a name="authentication-and-authorization-for-spas"></a>SPAs에 대 한 인증 및 권한 부여

ASP.NET Core 3.1 이상 템플릿은 API 권한 부여에 대 한 지원을 사용 하 여 SPAs (단일 페이지 앱)의 인증을 제공 합니다. ASP.NET Core Identity사용자를 인증 하 고 저장 하기 위해 Openid connect Connect를 구현 하기 위해 [ Identity 서버](https://identityserver.io/) 와 결합 됩니다.

인증 매개 변수는 **웹 응용 프로그램 (모델-뷰-컨트롤러)** (MVC) 및 **웹 응용 프로그램** (페이지) 프로젝트 템플릿의 인증 매개 변수와 비슷한 **각도** 및 **반응** 프로젝트 템플릿에 추가 되었습니다 Razor . 허용 되는 매개 변수 값은 **None** 및 **개인용** 입니다. 현재 **React.js 및 Redux** 프로젝트 템플릿에서 인증 매개 변수를 지원 하지 않습니다.

## <a name="create-an-app-with-api-authorization-support"></a>API 권한 부여를 지 원하는 앱 만들기

사용자 인증 및 권한 부여는 모두와 함께 사용할 수 있습니다. 명령 셸을 열고 다음 명령을 실행 합니다.

**각도** :

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**반응** :

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

위의 명령은 SPA를 포함 하는 *ClientApp* 디렉터리를 사용 하 여 ASP.NET Core 앱을 만듭니다.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>앱의 ASP.NET Core 구성 요소에 대 한 일반적인 설명

다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.

### <a name="startup-class"></a>시작 클래스

다음 코드 예제에서는 AspNetCore를 사용 합니다 [. Identity 서버](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet 패키지. 이 예제에서는 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 및 확장 메서드를 사용 하 여 API 인증 및 권한 부여를 구성 합니다 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> . 인증을 통해 반응 또는 각도 SPA 프로젝트 템플릿을 사용 하는 프로젝트에는이 패키지에 대 한 참조가 포함 됩니다.

클래스에는 `Startup` 다음과 같은 추가 항목이 있습니다.

* 메서드 내에서 `Startup.ConfigureServices` :
  * Identity 기본 UI 사용:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity`AddApiAuthorization`서버 위에 몇 가지 기본 ASP.NET Core 규칙을 설정 하는 추가 도우미 메서드가 포함 된 서버 Identity :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer에 의해 생성된 JWT 토큰의 유효성을 검사하도록 앱을 구성하는 추가 `AddIdentityServerJwt` 도우미 메서드를 사용한 인증:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* 메서드 내에서 `Startup.Configure` :
  * 요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 해야 하는 인증 미들웨어입니다.

    ```csharp
    app.UseAuthentication();
    ```

  * IdentityOpenid connect Connect 끝점을 노출 하는 서버 미들웨어:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

이 도우미 메서드는 Identity 지원 되는 구성을 사용 하도록 서버를 구성 합니다. IdentityServer는 앱 보안 문제를 처리하는 강력하고 확장성 있는 프레임워크입니다. 이와 동시에는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다. 따라서 규칙 집합 및 구성 옵션이 적절 한 시작 지점으로 간주 되는 사용자에 게 제공 됩니다. 인증을 변경 해야 하는 경우에는 요구 사항에 Identity 맞게 인증을 사용자 지정할 수 있도록 서버의 전체 기능을 계속 사용할 수 있습니다.

### <a name="addno-locidentityserverjwt"></a>AddIdentityServerJwt

이 도우미 메서드는 앱의 정책 스키마를 기본 인증 처리기로 구성 합니다. 이 정책은 Identity Identity URL 공간 "/"에서 모든 하위 경로로 라우팅되는 모든 요청을 처리할 수 있도록 구성 됩니다 Identity . `JwtBearerHandler`는 다른 모든 요청을 처리합니다. 또한이 메서드는 `<<ApplicationName>>API` 기본 범위를 사용 하는 서버에 API 리소스를 등록 하 Identity `<<ApplicationName>>API` 고 JWT 전달자 토큰 미들웨어를 구성 하 여 Identity 서버에서 앱에 대해 발급 한 토큰의 유효성을 검사 합니다.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

*Controllers\WeatherForecastController.cs* 파일에서 `[Authorize]` 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한을 부여 해야 함을 나타내는 클래스에 적용 된 특성을 확인 합니다. 기본 권한 부여 정책은 위에서 언급 한 정책 체계에 의해 설정 되는 기본 인증 체계를 사용 하도록 구성 되며, `AddIdentityServerJwt` `JwtBearerHandler` 이러한 도우미 메서드에서 앱에 대 한 요청의 기본 처리기로 구성 됩니다.

### <a name="applicationdbcontext"></a>ApplicationDbContext

*Data\ApplicationDbContext.cs* 파일에서는 `DbContext` 가 Identity 확장 하는 예외 `ApiAuthorizationDbContext` (에서 더 많이 파생 된 클래스)를 사용 `IdentityDbContext` 하 여 서버에 대 한 스키마를 포함 하 Identity 는 것을 확인 합니다.

데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 클래스 중 하나에서 상속 하 Identity `DbContext` 고 Identity 메서드를 호출 하 여 스키마를 포함 하도록 컨텍스트를 구성 합니다 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` .

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

*Controllers\OidcConfigurationController.cs* 파일에서 클라이언트가 사용 해야 하는 oidc 매개 변수를 제공 하도록 프로 비전 된 끝점을 확인 합니다.

### appsettings.json

*appsettings.json* 프로젝트 루트의 파일에는 `IdentityServer` 구성 된 클라이언트 목록을 설명 하는 새 섹션이 있습니다. 다음 예제에는 단일 클라이언트가 있습니다. 클라이언트 이름은 앱 이름에 대응되며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다. 프로필은 구성되는 앱 유형을 나타냅니다. 서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다. [응용 프로그램 프로필](#application-profiles) 섹션에 설명 된 대로 몇 가지 프로필을 사용할 수 있습니다.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.js

프로젝트 루트의 *appsettings.Development.js* 파일에는 `IdentityServer` 토큰에 서명 하는 데 사용 되는 키를 설명 하는 섹션이 있습니다. 프로덕션에 배포 하는 경우 [프로덕션에 배포](#deploy-to-production) 섹션에 설명 된 대로 키를 프로 비전 하 고 앱과 함께 배포 해야 합니다.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>각도 앱에 대 한 일반적인 설명

각도 템플릿에서 인증 및 API 권한 부여 지원은 *Clientapp\src\api\ac-authentication* 디렉터리의 자체 각도 모듈에 있습니다. 모듈은 다음 요소로 구성 됩니다.

* 3 개 구성 요소:
  * *login. component. ts* : 앱의 로그인 흐름을 처리 합니다.
  * *logout* . s a t: 응용 프로그램의 로그 아웃 흐름을 처리 합니다.
  * *login-menu. component* : 다음 링크 집합 중 하나를 표시 하는 위젯:
    * 사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.
    * 사용자가 인증 되지 않은 경우 등록 및 로그인 링크.
* 경로 `AuthorizeGuard` 에 추가할 수 있으며 경로를 방문 하기 전에 사용자를 인증 해야 하는 경로 가드입니다.
* `AuthorizeInterceptor`사용자가 인증 될 때 API를 대상으로 하는 나가는 http 요청에 액세스 토큰을 연결 하는 http 인터셉터입니다.
* `AuthorizeService`인증 프로세스의 하위 수준 세부 정보를 처리 하 고 사용을 위해 인증 된 사용자에 대 한 정보를 나머지 앱에 노출 하는 서비스입니다.
* 앱의 인증 부분과 연결 된 경로를 정의 하는 각도 모듈입니다. 응용 프로그램의 나머지 부분에서 사용 하기 위해 로그인 메뉴 구성 요소, 인터셉터, 가드 및 서비스를 노출 합니다.

## <a name="general-description-of-the-react-app"></a>반응 앱에 대 한 일반적인 설명

반응 템플릿에서 인증 및 API 권한 부여에 대 한 지원은 *ClientApp\src\components\api-authorization* 디렉터리에 있습니다. 다음 요소로 구성 됩니다.

* 4 구성 요소:
  * *Login.js* : 앱의 로그인 흐름을 처리 합니다.
  * *Logout.js* : 응용 프로그램의 로그 아웃 흐름을 처리 합니다.
  * *LoginMenu.js* : 다음 링크 집합 중 하나를 표시 하는 위젯:
    * 사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.
    * 사용자가 인증 되지 않은 경우 등록 및 로그인 링크.
  * *AuthorizeRoute.js* : 매개 변수에 표시 된 구성 요소를 렌더링 하기 전에 사용자를 인증 해야 하는 경로 구성 요소입니다 `Component` .
* `authService` `AuthorizeService` 인증 프로세스의 하위 수준 정보를 처리 하 고 인증 된 사용자에 대 한 정보를 사용 하기 위해 나머지 앱에 노출 하는 클래스의 내보낸 인스턴스입니다.

이제 솔루션의 주요 구성 요소를 살펴보았으므로 앱에 대 한 개별 시나리오를 자세히 살펴볼 수 있습니다.

## <a name="require-authorization-on-a-new-api"></a>새 API에 대 한 권한 부여 필요

기본적으로 시스템은 새 Api에 대 한 권한 부여를 쉽게 요구 하도록 구성 됩니다. 이렇게 하려면 새 컨트롤러를 만들고 컨트롤러 `[Authorize]` 클래스 또는 컨트롤러 내의 모든 작업에 특성을 추가 합니다.

## <a name="customize-the-api-authentication-handler"></a>API 인증 처리기 사용자 지정

API의 JWT 처리기 구성을 사용자 지정 하려면 해당 인스턴스를 구성 합니다 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> .

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

API의 JWT 처리기는를 사용 하 여 인증 프로세스를 제어할 수 있는 이벤트를 발생 시킵니다 `JwtBearerEvents` . API 권한 부여에 대 한 지원을 제공 하기 위해는 `AddIdentityServerJwt` 자체 이벤트 처리기를 등록 합니다.

이벤트 처리를 사용자 지정 하려면 필요한 만큼 추가 논리를 사용 하 여 기존 이벤트 처리기를 래핑합니다. 다음은 그 예입니다.

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

위의 코드에서 `OnTokenValidated` 이벤트 처리기는 사용자 지정 구현으로 대체 됩니다. 구현:

1. API 권한 부여 지원에서 제공 하는 원래 구현을 호출 합니다.
1. 고유한 사용자 지정 논리를 실행 합니다.

## <a name="protect-a-client-side-route-angular"></a>클라이언트 쪽 경로 (각도) 보호

클라이언트 쪽 경로 보호는 경로를 구성할 때 실행할 가드 목록에 권한 부여 가드를 추가 하 여 수행 됩니다. 예를 들어 `fetch-data` 기본 앱 각도 모듈 내에서 경로를 구성 하는 방법을 확인할 수 있습니다.

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

경로를 보호 하는 것은 실제 끝점 (계속 적용 되는 특성이 필요 함)을 보호 하지 `[Authorize]` 않지만 사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동할 수 없다는 것을 언급 하는 것이 중요 합니다.

## <a name="authenticate-api-requests-angular"></a>API 요청 (각도) 인증

앱과 함께 호스트 되는 Api에 대 한 요청 인증은 앱에서 정의 된 HTTP 클라이언트 인터셉터를 사용 하 여 자동으로 수행 됩니다.

## <a name="protect-a-client-side-route-react"></a>클라이언트 쪽 경로 보호 (반응)

`AuthorizeRoute`일반 구성 요소 대신 구성 요소를 사용 하 여 클라이언트 쪽 경로를 보호 `Route` 합니다. 예를 들어 `fetch-data` 구성 요소 내에서 경로를 구성 하는 방법을 확인 합니다 `App` .

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

경로 보호:

* 실제 끝점을 보호 하지 않습니다 (여전히 `[Authorize]` 특성이 적용 되어야 함).
* 사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동할 수 없습니다.

## <a name="authenticate-api-requests-react"></a>API 요청 인증 (반응)

응답을 사용 하 여 요청을 인증 하려면 먼저 `authService` 에서 인스턴스를 가져옵니다 `AuthorizeService` . 액세스 토큰은에서 검색 되 `authService` 고 아래와 같이 요청에 연결 됩니다. 구성 요소에 반응 하는 경우이 작업은 일반적으로 `componentDidMount` 수명 주기 메서드에서 수행 하거나 일부 사용자 조작의 결과로 수행 됩니다.

### <a name="import-the-authservice-into-your-component"></a>구성 요소로 authService 가져오기

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>액세스 토큰을 검색 하 고 응답에 연결 합니다.

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>프로덕션 환경에 배포

프로덕션 환경에 앱을 배포 하려면 다음 리소스를 프로 비전 해야 합니다.

* Identity사용자 계정 및 서버에서 부여할 데이터베이스를 저장 합니다 Identity .
* 토큰에 서명 하는 데 사용할 프로덕션 인증서입니다.
  * 이 인증서에 대 한 특정 요구 사항은 없습니다. 자체 서명 된 인증서 또는 CA 인증 기관을 통해 프로 비전 된 인증서 일 수 있습니다.
  * PowerShell 또는 OpenSSL 같은 표준 도구를 통해 생성할 수 있습니다.
  * 대상 컴퓨터의 인증서 저장소에 설치 하거나 강력한 암호를 사용 하 여 *.pfx* 파일로 배포할 수 있습니다.

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a>예: 비 Azure 웹 호스팅 공급자에 배포

웹 호스팅 패널에서 인증서를 만들거나 로드 합니다. 그런 다음 앱의 *appsettings.json* 파일에서 섹션을 수정 `IdentityServer` 하 여 키 세부 정보를 포함 합니다. 다음은 그 예입니다.

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

앞의 예제에서:

* `StoreName` 인증서가 저장 되는 인증서 저장소의 이름을 나타냅니다. 이 경우 웹 호스팅 저장소를 가리킵니다.
* `StoreLocation` 인증서를 로드할 위치를 나타냅니다 ( `CurrentUser` 이 경우).
* `Name` 인증서의 고유 주체에 해당 합니다.

### <a name="example-deploy-to-azure-app-service"></a>예: Azure App Service에 배포

이 섹션에서는 인증서 저장소에 저장 된 인증서를 사용 하 여 Azure App Service에 앱을 배포 하는 방법을 설명 합니다. 인증서 저장소에서 인증서를 로드 하도록 앱을 수정 하려면 이후 단계에서 Azure Portal에서 앱을 구성할 때 표준 계층 서비스 계획 이상이 필요 합니다.

앱의 파일에서 *appsettings.json* 섹션을 수정 `IdentityServer` 하 여 키 세부 정보를 포함 합니다.

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* 저장소 이름은 인증서가 저장 되는 인증서 저장소의 이름을 나타냅니다. 이 경우 개인 사용자 저장소를 가리킵니다.
* 저장소 위치는 (또는)에서 인증서를 로드할 위치를 나타냅니다 `CurrentUser` `LocalMachine` .
* 인증서의 이름 속성은 인증서의 고유 주체에 해당 합니다.

Azure App Service에 배포 하려면 [azure에 앱 배포](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)의 단계에 따라 필요한 azure 리소스를 만들고 프로덕션에 앱을 배포 하는 방법을 설명 합니다.

위의 지침을 수행 하면 앱이 Azure에 배포 되지만 아직 작동 하지 않습니다. 앱에서 사용 하는 인증서를 Azure Portal 구성 해야 합니다. 인증서에 대 한 지문을 찾고 [인증서 로드](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)에 설명 된 단계를 따릅니다.

이러한 단계에서는 SSL을 언급 하지만 앱에서 사용할 프로 비전 된 인증서를 업로드할 수 있는 Azure Portal **개인 인증서** 섹션이 있습니다.

Azure Portal에서 앱 및 앱 설정을 구성한 후 포털에서 앱을 다시 시작 합니다.

## <a name="other-configuration-options"></a>기타 구성 옵션

API 권한 부여에 대 한 지원은 Identity 규칙, 기본값 및 향상 된 기능 집합을 사용 하 여 서버를 기반으로 구축 되어 SPAs에 대 한 환경을 단순화 합니다. 물론 Identity ASP.NET Core 통합이 시나리오에 포함 되지 않은 경우 서버의 전체 기능을 백그라운드에서 사용할 수 있습니다. ASP.NET Core 지원은 조직에서 모든 앱을 만들고 배포 하는 "자사" 앱에 중점을 두었습니다. 따라서 동의 또는 페더레이션과 같은 항목에 대 한 지원이 제공 되지 않습니다. 이러한 시나리오의 경우 서버를 사용 Identity 하 고 해당 설명서를 따릅니다.

### <a name="application-profiles"></a>응용 프로그램 프로필

응용 프로그램 프로필은 매개 변수를 추가로 정의 하는 앱에 대 한 미리 정의 된 구성입니다. 이번에는 다음 프로필이 지원 됩니다.

* `IdentityServerSPA`: 단일 단위로 서버와 함께 호스트 된 SPA를 나타냅니다 Identity .
  * `redirect_uri`기본값은 `/authentication/login-callback` 입니다.
  * `post_logout_redirect_uri`기본값은 `/authentication/logout-callback` 입니다.
  * 범위 집합에는 `openid` `profile` 앱의 api에 대해 정의 된, 및 모든 범위가 포함 됩니다.
  * 허용 되는 OIDC 응답 형식의 집합은 `id_token token` 또는 각각 개별적으로 ( `id_token` , `token` )입니다.
  * 허용 되는 응답 모드는 `fragment` 입니다.
* `SPA`: 서버에서 호스팅되지 않는 SPA를 나타냅니다 Identity .
  * 범위 집합에는 `openid` `profile` 앱의 api에 대해 정의 된, 및 모든 범위가 포함 됩니다.
  * 허용 되는 OIDC 응답 형식의 집합은 `id_token token` 또는 각각 개별적으로 ( `id_token` , `token` )입니다.
  * 허용 되는 응답 모드는 `fragment` 입니다.
* `IdentityServerJwt`: 서버와 함께 호스트 되는 API를 나타냅니다 Identity .
  * 앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.
* `API`: 서버에서 호스팅되지 않는 API를 나타냅니다 Identity .
  * 앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.

### <a name="configuration-through-appsettings"></a>AppSettings를 통한 구성

구성 시스템을 또는의 목록에 추가 하 여 앱을 구성 합니다 `Clients` `Resources` .

다음 예제와 같이 각 클라이언트의 `redirect_uri` 및 `post_logout_redirect_uri` 속성을 구성 합니다.

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

리소스를 구성할 때 아래와 같이 리소스에 대 한 범위를 구성할 수 있습니다.

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>코드를 통한 구성

`AddApiAuthorization`옵션을 구성 하는 작업을 수행 하는 오버 로드를 사용 하 여 코드를 통해 클라이언트 및 리소스를 구성할 수도 있습니다.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>추가 리소스

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
