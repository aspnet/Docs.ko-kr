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
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658666"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="9872b-103">SPAs에 대 한 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="9872b-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="9872b-104">ASP.NET Core 3.1 이상 템플릿은 API 권한 부여에 대 한 지원을 사용 하 여 SPAs (단일 페이지 앱)의 인증을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="9872b-105">ASP.NET Core Identity사용자를 인증 하 고 저장 하기 위해 Openid connect Connect를 구현 하기 위해 [ Identity 서버](https://identityserver.io/) 와 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="9872b-106">인증 매개 변수는 **웹 응용 프로그램 (모델-뷰-컨트롤러)** (MVC) 및 **웹 응용 프로그램** (페이지) 프로젝트 템플릿의 인증 매개 변수와 비슷한 **각도** 및 **반응** 프로젝트 템플릿에 추가 되었습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="9872b-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="9872b-107">허용 되는 매개 변수 값은 **None** 및 **개인용** 입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="9872b-108">현재 **React.js 및 Redux** 프로젝트 템플릿에서 인증 매개 변수를 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="9872b-109">API 권한 부여를 지 원하는 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="9872b-109">Create an app with API authorization support</span></span>

<span data-ttu-id="9872b-110">사용자 인증 및 권한 부여는 모두와 함께 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="9872b-111">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="9872b-112">**각도**:</span><span class="sxs-lookup"><span data-stu-id="9872b-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="9872b-113">**반응**:</span><span class="sxs-lookup"><span data-stu-id="9872b-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="9872b-114">위의 명령은 SPA를 포함 하는 *ClientApp* 디렉터리를 사용 하 여 ASP.NET Core 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="9872b-115">앱의 ASP.NET Core 구성 요소에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="9872b-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="9872b-116">다음 섹션에서는 인증 지원을 포함 하는 경우 프로젝트에 대 한 추가 설명입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="9872b-117">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="9872b-117">Startup class</span></span>

<span data-ttu-id="9872b-118">다음 코드 예제에서는 AspNetCore를 사용 합니다 [. Identity 서버](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="9872b-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="9872b-119">이 예제에서는 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 및 확장 메서드를 사용 하 여 API 인증 및 권한 부여를 구성 합니다 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="9872b-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="9872b-120">인증을 통해 반응 또는 각도 SPA 프로젝트 템플릿을 사용 하는 프로젝트에는이 패키지에 대 한 참조가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="9872b-121">클래스에는 `Startup` 다음과 같은 추가 항목이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="9872b-122">메서드 내에서 `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9872b-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="9872b-123">Identity 기본 UI 사용:</span><span class="sxs-lookup"><span data-stu-id="9872b-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="9872b-124">Identity`AddApiAuthorization`서버 위에 몇 가지 기본 ASP.NET Core 규칙을 설정 하는 추가 도우미 메서드가 포함 된 서버 Identity :</span><span class="sxs-lookup"><span data-stu-id="9872b-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="9872b-125">IdentityServer에 의해 생성된 JWT 토큰의 유효성을 검사하도록 앱을 구성하는 추가 `AddIdentityServerJwt` 도우미 메서드를 사용한 인증:</span><span class="sxs-lookup"><span data-stu-id="9872b-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="9872b-126">메서드 내에서 `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9872b-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="9872b-127">요청 자격 증명의 유효성을 검사 하 고 요청 컨텍스트에서 사용자를 설정 해야 하는 인증 미들웨어입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="9872b-128">IdentityOpenid connect Connect 끝점을 노출 하는 서버 미들웨어:</span><span class="sxs-lookup"><span data-stu-id="9872b-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="9872b-129">Linux의 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="9872b-129">Azure App Service on Linux</span></span>

<span data-ttu-id="9872b-130">Linux에서 Azure App Service 배포의 경우에서 명시적으로 발급자를 지정 합니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="9872b-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="9872b-131">위의 코드에서 `{AUTHORITY}` 자리 표시자는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> openid connect Connect 호출을 만들 때 사용할입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="9872b-132">예:</span><span class="sxs-lookup"><span data-stu-id="9872b-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="9872b-133">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="9872b-133">AddApiAuthorization</span></span>

<span data-ttu-id="9872b-134">이 도우미 메서드는 Identity 지원 되는 구성을 사용 하도록 서버를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="9872b-135">IdentityServer는 앱 보안 문제를 처리하는 강력하고 확장성 있는 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="9872b-136">이와 동시에는 가장 일반적인 시나리오에 대 한 불필요 한 복잡성을 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="9872b-137">따라서 규칙 집합 및 구성 옵션이 적절 한 시작 지점으로 간주 되는 사용자에 게 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="9872b-138">인증을 변경 해야 하는 경우에는 요구 사항에 Identity 맞게 인증을 사용자 지정할 수 있도록 서버의 전체 기능을 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="9872b-139">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="9872b-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="9872b-140">이 도우미 메서드는 앱의 정책 스키마를 기본 인증 처리기로 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="9872b-141">이 정책은 Identity Identity URL 공간 "/"에서 모든 하위 경로로 라우팅되는 모든 요청을 처리할 수 있도록 구성 됩니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="9872b-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="9872b-142">`JwtBearerHandler`는 다른 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="9872b-143">또한이 메서드는 `<<ApplicationName>>API` 기본 범위를 사용 하는 서버에 API 리소스를 등록 하 Identity `<<ApplicationName>>API` 고 JWT 전달자 토큰 미들웨어를 구성 하 여 Identity 서버에서 앱에 대해 발급 한 토큰의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="9872b-144">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="9872b-144">WeatherForecastController</span></span>

<span data-ttu-id="9872b-145">*Controllers\WeatherForecastController.cs* 파일에서 `[Authorize]` 리소스에 액세스 하기 위한 기본 정책에 따라 사용자에 게 권한을 부여 해야 함을 나타내는 클래스에 적용 된 특성을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="9872b-146">기본 권한 부여 정책은 위에서 언급 한 정책 체계에 의해 설정 되는 기본 인증 체계를 사용 하도록 구성 되며, `AddIdentityServerJwt` `JwtBearerHandler` 이러한 도우미 메서드에서 앱에 대 한 요청의 기본 처리기로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="9872b-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="9872b-147">ApplicationDbContext</span></span>

<span data-ttu-id="9872b-148">*Data\ApplicationDbContext.cs* 파일에서는 `DbContext` 가 Identity 확장 하는 예외 `ApiAuthorizationDbContext` (에서 더 많이 파생 된 클래스)를 사용 `IdentityDbContext` 하 여 서버에 대 한 스키마를 포함 하 Identity 는 것을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="9872b-149">데이터베이스 스키마에 대 한 모든 권한을 얻으려면 사용 가능한 클래스 중 하나에서 상속 하 Identity `DbContext` 고 Identity 메서드를 호출 하 여 스키마를 포함 하도록 컨텍스트를 구성 합니다 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="9872b-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="9872b-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="9872b-150">OidcConfigurationController</span></span>

<span data-ttu-id="9872b-151">*Controllers\OidcConfigurationController.cs* 파일에서 클라이언트가 사용 해야 하는 oidc 매개 변수를 제공 하도록 프로 비전 된 끝점을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="9872b-152">*appsettings.json* 프로젝트 루트의 파일에는 `IdentityServer` 구성 된 클라이언트 목록을 설명 하는 새 섹션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="9872b-153">다음 예제에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-153">In the following example, there's a single client.</span></span> <span data-ttu-id="9872b-154">클라이언트 이름은 앱 이름에 대응되며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="9872b-155">프로필은 구성되는 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="9872b-156">서버에 대 한 구성 프로세스를 간소화 하는 규칙을 구동 하기 위해 내부적으로 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="9872b-157">[응용 프로그램 프로필](#application-profiles) 섹션에 설명 된 대로 몇 가지 프로필을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="9872b-158">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="9872b-158">appsettings.Development.json</span></span>

<span data-ttu-id="9872b-159">프로젝트 루트의 *appsettings.Development.js* 파일에는 `IdentityServer` 토큰에 서명 하는 데 사용 되는 키를 설명 하는 섹션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="9872b-160">프로덕션에 배포 하는 경우 [프로덕션에 배포](#deploy-to-production) 섹션에 설명 된 대로 키를 프로 비전 하 고 앱과 함께 배포 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="9872b-161">각도 앱에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="9872b-161">General description of the Angular app</span></span>

<span data-ttu-id="9872b-162">각도 템플릿에서 인증 및 API 권한 부여 지원은 *Clientapp\src\api\ac-authentication* 디렉터리의 자체 각도 모듈에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="9872b-163">모듈은 다음 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="9872b-164">3 개 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="9872b-164">3 components:</span></span>
  * <span data-ttu-id="9872b-165">*login. component. ts*: 앱의 로그인 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="9872b-166">*logout*. s a t: 응용 프로그램의 로그 아웃 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="9872b-167">*login-menu. component*: 다음 링크 집합 중 하나를 표시 하는 위젯:</span><span class="sxs-lookup"><span data-stu-id="9872b-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="9872b-168">사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="9872b-169">사용자가 인증 되지 않은 경우 등록 및 로그인 링크.</span><span class="sxs-lookup"><span data-stu-id="9872b-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="9872b-170">경로 `AuthorizeGuard` 에 추가할 수 있으며 경로를 방문 하기 전에 사용자를 인증 해야 하는 경로 가드입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="9872b-171">`AuthorizeInterceptor`사용자가 인증 될 때 API를 대상으로 하는 나가는 http 요청에 액세스 토큰을 연결 하는 http 인터셉터입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="9872b-172">`AuthorizeService`인증 프로세스의 하위 수준 세부 정보를 처리 하 고 사용을 위해 인증 된 사용자에 대 한 정보를 나머지 앱에 노출 하는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="9872b-173">앱의 인증 부분과 연결 된 경로를 정의 하는 각도 모듈입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="9872b-174">응용 프로그램의 나머지 부분에서 사용 하기 위해 로그인 메뉴 구성 요소, 인터셉터, 가드 및 서비스를 노출 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="9872b-175">반응 앱에 대 한 일반적인 설명</span><span class="sxs-lookup"><span data-stu-id="9872b-175">General description of the React app</span></span>

<span data-ttu-id="9872b-176">반응 템플릿에서 인증 및 API 권한 부여에 대 한 지원은 *ClientApp\src\components\api-authorization* 디렉터리에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="9872b-177">다음 요소로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="9872b-178">4 구성 요소:</span><span class="sxs-lookup"><span data-stu-id="9872b-178">4 components:</span></span>
  * <span data-ttu-id="9872b-179">*Login.js*: 앱의 로그인 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="9872b-180">*Logout.js*: 응용 프로그램의 로그 아웃 흐름을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="9872b-181">*LoginMenu.js*: 다음 링크 집합 중 하나를 표시 하는 위젯:</span><span class="sxs-lookup"><span data-stu-id="9872b-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="9872b-182">사용자가 인증 되 면 사용자 프로필 관리 및 로그 아웃 링크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="9872b-183">사용자가 인증 되지 않은 경우 등록 및 로그인 링크.</span><span class="sxs-lookup"><span data-stu-id="9872b-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="9872b-184">*AuthorizeRoute.js*: 매개 변수에 표시 된 구성 요소를 렌더링 하기 전에 사용자를 인증 해야 하는 경로 구성 요소입니다 `Component` .</span><span class="sxs-lookup"><span data-stu-id="9872b-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="9872b-185">`authService` `AuthorizeService` 인증 프로세스의 하위 수준 정보를 처리 하 고 인증 된 사용자에 대 한 정보를 사용 하기 위해 나머지 앱에 노출 하는 클래스의 내보낸 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="9872b-186">이제 솔루션의 주요 구성 요소를 살펴보았으므로 앱에 대 한 개별 시나리오를 자세히 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="9872b-187">새 API에 대 한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="9872b-187">Require authorization on a new API</span></span>

<span data-ttu-id="9872b-188">기본적으로 시스템은 새 Api에 대 한 권한 부여를 쉽게 요구 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="9872b-189">이렇게 하려면 새 컨트롤러를 만들고 컨트롤러 `[Authorize]` 클래스 또는 컨트롤러 내의 모든 작업에 특성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="9872b-190">API 인증 처리기 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="9872b-190">Customize the API authentication handler</span></span>

<span data-ttu-id="9872b-191">API의 JWT 처리기 구성을 사용자 지정 하려면 해당 인스턴스를 구성 합니다 <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> .</span><span class="sxs-lookup"><span data-stu-id="9872b-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

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

<span data-ttu-id="9872b-192">API의 JWT 처리기는를 사용 하 여 인증 프로세스를 제어할 수 있는 이벤트를 발생 시킵니다 `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="9872b-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="9872b-193">API 권한 부여에 대 한 지원을 제공 하기 위해는 `AddIdentityServerJwt` 자체 이벤트 처리기를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="9872b-194">이벤트 처리를 사용자 지정 하려면 필요한 만큼 추가 논리를 사용 하 여 기존 이벤트 처리기를 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="9872b-195">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="9872b-195">For example:</span></span>

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

<span data-ttu-id="9872b-196">위의 코드에서 `OnTokenValidated` 이벤트 처리기는 사용자 지정 구현으로 대체 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="9872b-197">구현:</span><span class="sxs-lookup"><span data-stu-id="9872b-197">This implementation:</span></span>

1. <span data-ttu-id="9872b-198">API 권한 부여 지원에서 제공 하는 원래 구현을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="9872b-199">고유한 사용자 지정 논리를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="9872b-200">클라이언트 쪽 경로 (각도) 보호</span><span class="sxs-lookup"><span data-stu-id="9872b-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="9872b-201">클라이언트 쪽 경로 보호는 경로를 구성할 때 실행할 가드 목록에 권한 부여 가드를 추가 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="9872b-202">예를 들어 `fetch-data` 기본 앱 각도 모듈 내에서 경로를 구성 하는 방법을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="9872b-203">경로를 보호 하는 것은 실제 끝점 (계속 적용 되는 특성이 필요 함)을 보호 하지 `[Authorize]` 않지만 사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동할 수 없다는 것을 언급 하는 것이 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="9872b-204">API 요청 (각도) 인증</span><span class="sxs-lookup"><span data-stu-id="9872b-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="9872b-205">앱과 함께 호스트 되는 Api에 대 한 요청 인증은 앱에서 정의 된 HTTP 클라이언트 인터셉터를 사용 하 여 자동으로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="9872b-206">클라이언트 쪽 경로 보호 (반응)</span><span class="sxs-lookup"><span data-stu-id="9872b-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="9872b-207">`AuthorizeRoute`일반 구성 요소 대신 구성 요소를 사용 하 여 클라이언트 쪽 경로를 보호 `Route` 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="9872b-208">예를 들어 `fetch-data` 구성 요소 내에서 경로를 구성 하는 방법을 확인 합니다 `App` .</span><span class="sxs-lookup"><span data-stu-id="9872b-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="9872b-209">경로 보호:</span><span class="sxs-lookup"><span data-stu-id="9872b-209">Protecting a route:</span></span>

* <span data-ttu-id="9872b-210">실제 끝점을 보호 하지 않습니다 (여전히 `[Authorize]` 특성이 적용 되어야 함).</span><span class="sxs-lookup"><span data-stu-id="9872b-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="9872b-211">사용자가 인증 되지 않은 경우 지정 된 클라이언트 쪽 경로로 이동할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="9872b-212">API 요청 인증 (반응)</span><span class="sxs-lookup"><span data-stu-id="9872b-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="9872b-213">응답을 사용 하 여 요청을 인증 하려면 먼저 `authService` 에서 인스턴스를 가져옵니다 `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="9872b-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="9872b-214">액세스 토큰은에서 검색 되 `authService` 고 아래와 같이 요청에 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="9872b-215">구성 요소에 반응 하는 경우이 작업은 일반적으로 `componentDidMount` 수명 주기 메서드에서 수행 하거나 일부 사용자 조작의 결과로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="9872b-216">구성 요소로 authService 가져오기</span><span class="sxs-lookup"><span data-stu-id="9872b-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="9872b-217">액세스 토큰을 검색 하 고 응답에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-217">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="9872b-218">프로덕션 환경에 배포</span><span class="sxs-lookup"><span data-stu-id="9872b-218">Deploy to production</span></span>

<span data-ttu-id="9872b-219">프로덕션 환경에 앱을 배포 하려면 다음 리소스를 프로 비전 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="9872b-220">Identity사용자 계정 및 서버에서 부여할 데이터베이스를 저장 합니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="9872b-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="9872b-221">토큰에 서명 하는 데 사용할 프로덕션 인증서입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="9872b-222">이 인증서에 대 한 특정 요구 사항은 없습니다. 자체 서명 된 인증서 또는 CA 인증 기관을 통해 프로 비전 된 인증서 일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="9872b-223">PowerShell 또는 OpenSSL 같은 표준 도구를 통해 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="9872b-224">대상 컴퓨터의 인증서 저장소에 설치 하거나 강력한 암호를 사용 하 여 *.pfx* 파일로 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="9872b-225">예: 비 Azure 웹 호스팅 공급자에 배포</span><span class="sxs-lookup"><span data-stu-id="9872b-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="9872b-226">웹 호스팅 패널에서 인증서를 만들거나 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="9872b-227">그런 다음 앱의 *appsettings.json* 파일에서 섹션을 수정 `IdentityServer` 하 여 키 세부 정보를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="9872b-228">다음은 그 예입니다. </span><span class="sxs-lookup"><span data-stu-id="9872b-228">For example:</span></span>

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

<span data-ttu-id="9872b-229">앞의 예제에서:</span><span class="sxs-lookup"><span data-stu-id="9872b-229">In the preceding example:</span></span>

* <span data-ttu-id="9872b-230">`StoreName` 인증서가 저장 되는 인증서 저장소의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="9872b-231">이 경우 웹 호스팅 저장소를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="9872b-232">`StoreLocation` 인증서를 로드할 위치를 나타냅니다 ( `CurrentUser` 이 경우).</span><span class="sxs-lookup"><span data-stu-id="9872b-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="9872b-233">`Name` 인증서의 고유 주체에 해당 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="9872b-234">예: Azure App Service에 배포</span><span class="sxs-lookup"><span data-stu-id="9872b-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="9872b-235">이 섹션에서는 인증서 저장소에 저장 된 인증서를 사용 하 여 Azure App Service에 앱을 배포 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="9872b-236">인증서 저장소에서 인증서를 로드 하도록 앱을 수정 하려면 이후 단계에서 Azure Portal에서 앱을 구성할 때 표준 계층 서비스 계획 이상이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="9872b-237">앱의 파일에서 *appsettings.json* 섹션을 수정 `IdentityServer` 하 여 키 세부 정보를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="9872b-238">저장소 이름은 인증서가 저장 되는 인증서 저장소의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="9872b-239">이 경우 개인 사용자 저장소를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="9872b-240">저장소 위치는 (또는)에서 인증서를 로드할 위치를 나타냅니다 `CurrentUser` `LocalMachine` .</span><span class="sxs-lookup"><span data-stu-id="9872b-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="9872b-241">인증서의 이름 속성은 인증서의 고유 주체에 해당 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="9872b-242">Azure App Service에 배포 하려면 [azure에 앱 배포](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)의 단계에 따라 필요한 azure 리소스를 만들고 프로덕션에 앱을 배포 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="9872b-243">위의 지침을 수행 하면 앱이 Azure에 배포 되지만 아직 작동 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="9872b-244">앱에서 사용 하는 인증서를 Azure Portal 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="9872b-245">인증서에 대 한 지문을 찾고 [인증서 로드](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)에 설명 된 단계를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="9872b-246">이러한 단계에서는 SSL을 언급 하지만 앱에서 사용할 프로 비전 된 인증서를 업로드할 수 있는 Azure Portal **개인 인증서** 섹션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="9872b-247">Azure Portal에서 앱 및 앱 설정을 구성한 후 포털에서 앱을 다시 시작 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="9872b-248">기타 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="9872b-248">Other configuration options</span></span>

<span data-ttu-id="9872b-249">API 권한 부여에 대 한 지원은 Identity 규칙, 기본값 및 향상 된 기능 집합을 사용 하 여 서버를 기반으로 구축 되어 SPAs에 대 한 환경을 단순화 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="9872b-250">물론 Identity ASP.NET Core 통합이 시나리오에 포함 되지 않은 경우 서버의 전체 기능을 백그라운드에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="9872b-251">ASP.NET Core 지원은 조직에서 모든 앱을 만들고 배포 하는 "자사" 앱에 중점을 두었습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="9872b-252">따라서 동의 또는 페더레이션과 같은 항목에 대 한 지원이 제공 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="9872b-253">이러한 시나리오의 경우 서버를 사용 Identity 하 고 해당 설명서를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="9872b-254">응용 프로그램 프로필</span><span class="sxs-lookup"><span data-stu-id="9872b-254">Application profiles</span></span>

<span data-ttu-id="9872b-255">응용 프로그램 프로필은 매개 변수를 추가로 정의 하는 앱에 대 한 미리 정의 된 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="9872b-256">이번에는 다음 프로필이 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="9872b-257">`IdentityServerSPA`: 단일 단위로 서버와 함께 호스트 된 SPA를 나타냅니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="9872b-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="9872b-258">`redirect_uri`기본값은 `/authentication/login-callback` 입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="9872b-259">`post_logout_redirect_uri`기본값은 `/authentication/logout-callback` 입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="9872b-260">범위 집합에는 `openid` `profile` 앱의 api에 대해 정의 된, 및 모든 범위가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="9872b-261">허용 되는 OIDC 응답 형식의 집합은 `id_token token` 또는 각각 개별적으로 ( `id_token` , `token` )입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="9872b-262">허용 되는 응답 모드는 `fragment` 입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="9872b-263">`SPA`: 서버에서 호스팅되지 않는 SPA를 나타냅니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="9872b-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="9872b-264">범위 집합에는 `openid` `profile` 앱의 api에 대해 정의 된, 및 모든 범위가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="9872b-265">허용 되는 OIDC 응답 형식의 집합은 `id_token token` 또는 각각 개별적으로 ( `id_token` , `token` )입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="9872b-266">허용 되는 응답 모드는 `fragment` 입니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="9872b-267">`IdentityServerJwt`: 서버와 함께 호스트 되는 API를 나타냅니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="9872b-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="9872b-268">앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="9872b-269">`API`: 서버에서 호스팅되지 않는 API를 나타냅니다 Identity .</span><span class="sxs-lookup"><span data-stu-id="9872b-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="9872b-270">앱은 앱 이름으로 기본 설정 된 단일 범위를 갖도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="9872b-271">AppSettings를 통한 구성</span><span class="sxs-lookup"><span data-stu-id="9872b-271">Configuration through AppSettings</span></span>

<span data-ttu-id="9872b-272">구성 시스템을 또는의 목록에 추가 하 여 앱을 구성 합니다 `Clients` `Resources` .</span><span class="sxs-lookup"><span data-stu-id="9872b-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="9872b-273">다음 예제와 같이 각 클라이언트의 `redirect_uri` 및 `post_logout_redirect_uri` 속성을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="9872b-274">리소스를 구성할 때 아래와 같이 리소스에 대 한 범위를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="9872b-275">코드를 통한 구성</span><span class="sxs-lookup"><span data-stu-id="9872b-275">Configuration through code</span></span>

<span data-ttu-id="9872b-276">`AddApiAuthorization`옵션을 구성 하는 작업을 수행 하는 오버 로드를 사용 하 여 코드를 통해 클라이언트 및 리소스를 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9872b-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="9872b-277">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="9872b-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
