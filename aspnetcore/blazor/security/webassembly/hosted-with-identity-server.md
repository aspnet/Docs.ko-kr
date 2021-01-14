---
title: Identity 서버를 사용하여 호스트된 ASP.NET Core Blazor WebAssembly 앱 보호
author: guardrex
description: Identity Server를 사용하여 호스트된 ASP.NET Core Blazor WebAssembly 앱을 보호하는 방법을 알아봅니다.
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: fdd7eb3c4a3b07022760a43cbde80838bfaf7c84
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024797"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="7f48b-103">Identity 서버를 사용하여 ASP.NET Core Blazor WebAssembly 호스트된 앱 보호</span><span class="sxs-lookup"><span data-stu-id="7f48b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="7f48b-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7f48b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7f48b-105">이 문서에서는 [IdentityServer](https://identityserver.io/)를 사용하여 사용자와 API 호출을 인증하는 ‘[호스트형 Blazor WebAssembly 앱](xref:blazor/hosting-models#blazor-webassembly)’을 만드는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="7f48b-106">독립형 또는 호스트형 Blazor WebAssembly 앱을 구성하여 기존의 외부 Identity 서버 인스턴스를 사용하려면 <xref:blazor/security/webassembly/standalone-with-authentication-library>의 지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="7f48b-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7f48b-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f48b-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f48b-108">인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면</span><span class="sxs-lookup"><span data-stu-id="7f48b-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="7f48b-109">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor WebAssembly 앱** 템플릿을 선택한 후 **인증** 에서 **변경** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="7f48b-110">**개별 사용자 계정** 을 **사용자 계정 앱 내 저장** 옵션과 함께 선택하여 ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용해 앱 내에 사용자를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="7f48b-111">**고급**  섹션에서 **ASP.NET Core 호스트형** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="7f48b-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7f48b-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="7f48b-113">인증 메커니즘을 사용하여 빈 폴더에 새 Blazor WebAssembly 프로젝트를 만들려면 `-au|--auth` 옵션으로 `Individual` 인증 메커니즘을 지정하여 ASP.NET Core의 [Identity](xref:security/authentication/identity) 시스템을 사용해 앱 내에 사용자를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="7f48b-114">자리표시자</span><span class="sxs-lookup"><span data-stu-id="7f48b-114">Placeholder</span></span>  | <span data-ttu-id="7f48b-115">예제</span><span class="sxs-lookup"><span data-stu-id="7f48b-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="7f48b-116">`-o|--output` 옵션으로 지정된 출력 위치는 프로젝트 폴더가 없는 경우 폴더를 하나 만들고 앱 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="7f48b-117">자세한 내용은 .NET Core 가이드의 [`dotnet new`](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f48b-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7f48b-118">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f48b-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f48b-119">인증 메커니즘을 사용하여 새 Blazor WebAssembly 프로젝트를 만들려면</span><span class="sxs-lookup"><span data-stu-id="7f48b-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="7f48b-120">**새 Blazor WebAssembly 앱 구성** 단계의 **인증** 드롭다운에서 **개별 인증(앱 내)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="7f48b-121">ASP.NET Core [Identity](xref:security/authentication/identity)를 사용하여 앱에 저장된 개별 사용자에 대한 앱이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="7f48b-122">**ASP.NET Core 호스트형** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="7f48b-123">*`Server`* 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7f48b-123">*`Server`* app configuration</span></span>

<span data-ttu-id="7f48b-124">다음 섹션에서는 인증 지원이 포함된 경우 프로젝트의 추가 사항에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="7f48b-125">시작 클래스</span><span class="sxs-lookup"><span data-stu-id="7f48b-125">Startup class</span></span>

<span data-ttu-id="7f48b-126">`Startup` 클래스에는 다음과 같은 추가 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="7f48b-127">`Startup.ConfigureServices`의 경우</span><span class="sxs-lookup"><span data-stu-id="7f48b-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="7f48b-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="7f48b-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="7f48b-129">IdentityServer 및 IdentityServer 위에 기본 ASP.NET Core 규칙을 설정하는 추가 <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드:</span><span class="sxs-lookup"><span data-stu-id="7f48b-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="7f48b-130">IdentityServer에 의해 생성된 JWT 토큰의 유효성을 검사하도록 앱을 구성하는 추가 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드를 사용한 인증:</span><span class="sxs-lookup"><span data-stu-id="7f48b-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="7f48b-131">`Startup.Configure`의 경우</span><span class="sxs-lookup"><span data-stu-id="7f48b-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="7f48b-132">IdentityServer 미들웨어가 OIDC(OpenID Connect) 엔드포인트를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="7f48b-133">인증 미들웨어는 요청 자격 증명의 유효성을 검사하고 요청 컨텍스트에서 사용자를 설정하는 작업을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="7f48b-134">인증 미들웨어가 인증 기능을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="7f48b-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7f48b-135">AddApiAuthorization</span></span>

<span data-ttu-id="7f48b-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> 도우미 메서드는 ASP.NET Core 시나리오에 대해 [IdentityServer](https://identityserver.io/)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="7f48b-137">IdentityServer는 앱 보안 문제를 처리하는 강력하고 확장성 있는 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="7f48b-138">IdentityServer는 대부분의 일반적인 시나리오에서 불필요한 복잡성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="7f48b-139">따라서 좋은 출발점이라고 생각되는 몇 가지 규칙과 구성 옵션 세트가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="7f48b-140">인증에 변경이 필요할 경우 IdentityServer의 모든 기능을 사용하여 앱의 요구 사항에 맞게 인증을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="7f48b-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="7f48b-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="7f48b-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> 도우미 메서드는 앱의 정책 체계를 기본 인증 처리기로서 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="7f48b-143">정책은 Identity가 Identity URL 공간 `/Identity`에 있는 모든 하위 경로로 라우팅된 모든 요청을 처리할 수 있도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="7f48b-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>는 다른 모든 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="7f48b-145">이 메서드는 또한</span><span class="sxs-lookup"><span data-stu-id="7f48b-145">Additionally, this method:</span></span>

* <span data-ttu-id="7f48b-146">기본 범위 `{APPLICATION NAME}API`를 사용하여 `{APPLICATION NAME}API` API 리소스를 IdentityServer에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="7f48b-147">JWT 전달자 토큰 미들웨어가 앱에 대해 IdentityServer에서 발행한 토큰의 유효성을 검사하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="7f48b-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="7f48b-148">WeatherForecastController</span></span>

<span data-ttu-id="7f48b-149">`WeatherForecastController`(`Controllers/WeatherForecastController.cs`)에서 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성이 클래스에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="7f48b-150">이 특성은 사용자가 리소스에 액세스하려면 기본 정책을 기준으로 인증되어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7f48b-151">기본 인증 정책은 기본 인증 체계를 사용하도록 구성되었으며, 기본 인증 체계는 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>에 의해 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="7f48b-152">도우미 메서드는 <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>를 앱에 대한 요청의 기본 처리기로 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7f48b-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7f48b-153">ApplicationDbContext</span></span>

<span data-ttu-id="7f48b-154">`ApplicationDbContext`(`Data/ApplicationDbContext.cs`)에서 <xref:Microsoft.EntityFrameworkCore.DbContext>는 IdentityServer에 대한 스키마를 포함하도록 <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="7f48b-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>는 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>에서 파생됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="7f48b-156">데이터베이스 스키마에 대한 모든 권한을 얻으려면 사용 가능한 Identity <xref:Microsoft.EntityFrameworkCore.DbContext> 클래스 중 하나에서 상속하고 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> 메서드에서 `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`를 호출하여 Identity 스키마를 포함하도록 컨텍스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7f48b-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7f48b-157">OidcConfigurationController</span></span>

<span data-ttu-id="7f48b-158">`OidcConfigurationController`(`Controllers/OidcConfigurationController.cs`)에서 클라이언트 엔드포인트가 OIDC 매개 변수를 제공하도록 프로비저닝됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="7f48b-159">앱 설정</span><span class="sxs-lookup"><span data-stu-id="7f48b-159">App settings</span></span>

<span data-ttu-id="7f48b-160">프로젝트 루트에 있는 앱 설정 파일(`appsettings.json`)의 `IdentityServer` 섹션은 구성된 클라이언트 목록을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="7f48b-161">다음 예제에는 단일 클라이언트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-161">In the following example, there's a single client.</span></span> <span data-ttu-id="7f48b-162">클라이언트 이름은 앱 이름에 대응되며 규칙에 따라 OAuth `ClientId` 매개 변수에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="7f48b-163">프로필은 구성되는 앱 유형을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="7f48b-164">프로필은 서버에 대한 구성 프로세스를 간소화하는 규칙을 구현하기 위해 내부적으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="7f48b-165">자리 표시자 `{APP ASSEMBLY}`는 앱의 어셈블리 이름입니다(예: `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="7f48b-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="7f48b-166">*`Client`* 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7f48b-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7f48b-167">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="7f48b-167">Authentication package</span></span>

<span data-ttu-id="7f48b-168">앱이 개별 사용자 계정을 사용하도록 만들어진 경우(`Individual`) 해당 앱은 앱의 프로젝트 파일에서 자동으로 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지에 대한 패키지 참조를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="7f48b-169">패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 데 사용할 토큰을 가져올 수 있도록 지원하는 기본 형식 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7f48b-170">앱에 인증을 추가하는 경우에는 패키지를 앱의 프로젝트 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="7f48b-171">자리 표시자 `{VERSION}`의 경우 애플리케이션의 공유 프레임워크 버전과 일치하는 안정적인 최신 버전의 패키지를 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)의 패키지 **버전 기록** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="7f48b-172">`HttpClient` 구성</span><span class="sxs-lookup"><span data-stu-id="7f48b-172">`HttpClient` configuration</span></span>

<span data-ttu-id="7f48b-173">`Program.Main`(`Program.cs`)에서 명명된 <xref:System.Net.Http.HttpClient>(`HostIS.ServerAPI`)는 서버 API에 요청할 때 액세스 토큰을 포함하는 <xref:System.Net.Http.HttpClient> 인스턴스를 제공하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="7f48b-174">호스트형 Blazor 솔루션의 일부가 아닌 기존 Identity 서버 인스턴스를 사용하도록 Blazor WebAssembly 앱을 구성하는 경우, <xref:System.Net.Http.HttpClient> 기준 주소 등록을 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>(`builder.HostEnvironment.BaseAddress`)에서 서버 앱의 API 권한 부여 엔드포인트 URL로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="7f48b-175">API 권한 부여 지원</span><span class="sxs-lookup"><span data-stu-id="7f48b-175">API authorization support</span></span>

<span data-ttu-id="7f48b-176">사용자 인증에 대한 지원은 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 패키지 내에서 제공하는 확장 메서드를 통해 서비스 컨테이너에 연결되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="7f48b-177">이 메서드는 앱이 기존 인증 시스템과 상호 작용하는 데 필요한 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="7f48b-178">기본적으로 앱에 대한 구성은 규칙에 따라 `_configuration/{client-id}`에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="7f48b-179">규칙에 따라 클라이언트 ID는 앱의 어셈블리 이름으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="7f48b-180">이 URL은 옵션을 사용하여 오버로드를 호출함으로써 별도의 엔드포인트를 가리키도록 변경될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="7f48b-181">Imports 파일</span><span class="sxs-lookup"><span data-stu-id="7f48b-181">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7f48b-182">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="7f48b-182">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="7f48b-183">App 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7f48b-183">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7f48b-184">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7f48b-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7f48b-185">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7f48b-185">LoginDisplay component</span></span>

<span data-ttu-id="7f48b-186">`LoginDisplay` 구성 요소(`Shared/LoginDisplay.razor`)는 `MainLayout` 구성 요소(`Shared/MainLayout.razor`)에서 렌더링되고 다음 동작을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="7f48b-187">인증된 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="7f48b-187">For authenticated users:</span></span>
  * <span data-ttu-id="7f48b-188">현재 사용자 이름을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-188">Displays the current user name.</span></span>
  * <span data-ttu-id="7f48b-189">ASP.NET Core Identity에 있는 사용자 프로필 페이지로 연결되는 링크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="7f48b-190">앱에서 로그아웃하는 단추를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="7f48b-191">익명 사용자의 경우:</span><span class="sxs-lookup"><span data-stu-id="7f48b-191">For anonymous users:</span></span>
  * <span data-ttu-id="7f48b-192">등록 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-192">Offers the option to register.</span></span>
  * <span data-ttu-id="7f48b-193">로그인 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="7f48b-194">Authentication 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7f48b-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7f48b-195">FetchData 구성 요소</span><span class="sxs-lookup"><span data-stu-id="7f48b-195">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7f48b-196">앱 실행</span><span class="sxs-lookup"><span data-stu-id="7f48b-196">Run the app</span></span>

<span data-ttu-id="7f48b-197">서버 프로젝트에서 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-197">Run the app from the Server project.</span></span> <span data-ttu-id="7f48b-198">Visual Studio를 사용하는 경우 다음 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="7f48b-199">도구 모음의 **시작 프로젝트** 드롭다운 목록을 ‘서버 API 앱’으로 설정하고 **실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="7f48b-200">**솔루션 탐색기** 에서 서버 프로젝트를 선택하고 도구 모음에서 **실행** 단추를 선택하거나 **디버그** 메뉴에서 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="7f48b-201">API 권한 부여를 사용한 이름 및 역할 클레임</span><span class="sxs-lookup"><span data-stu-id="7f48b-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="7f48b-202">사용자 지정 사용자 팩터리</span><span class="sxs-lookup"><span data-stu-id="7f48b-202">Custom user factory</span></span>

<span data-ttu-id="7f48b-203">*`Client`* 앱에서 사용자 지정 사용자 팩터리를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="7f48b-204">Identity 서버가 단일 `role` 클레임에서 여러 역할을 JSON 배열로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="7f48b-205">클레임에서 단일 역할이 문자열 값으로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="7f48b-206">팩터리가 각 사용자 역할에 대해 개별 `role` 클레임을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="7f48b-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="7f48b-207">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="7f48b-208">*`Client`* 앱에서 `Program.Main`(`Program.cs`)에 팩터리를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="7f48b-209">*`Server`* 앱에서 Identity 작성기에 대해 <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*>를 호출하여 역할 관련 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="7f48b-210">Identity Server 구성</span><span class="sxs-lookup"><span data-stu-id="7f48b-210">Configure Identity Server</span></span>

<span data-ttu-id="7f48b-211">다음 방법 중 **하나** 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="7f48b-212">API 권한 부여 옵션</span><span class="sxs-lookup"><span data-stu-id="7f48b-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="7f48b-213">프로필 서비스</span><span class="sxs-lookup"><span data-stu-id="7f48b-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="7f48b-214">API 권한 부여 옵션</span><span class="sxs-lookup"><span data-stu-id="7f48b-214">API authorization options</span></span>

<span data-ttu-id="7f48b-215">*`Server`* 앱에서:</span><span class="sxs-lookup"><span data-stu-id="7f48b-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="7f48b-216">Identity 서버가 `name` 및 `role` 클레임을 ID 토큰 및 액세스 토큰에 배치하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="7f48b-217">JWT 토큰 처리기에서 역할의 기본 매핑을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="7f48b-218">프로필 서비스</span><span class="sxs-lookup"><span data-stu-id="7f48b-218">Profile Service</span></span>

<span data-ttu-id="7f48b-219">*`Server`* 앱에서 `ProfileService` 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="7f48b-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="7f48b-220">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="7f48b-221">*`Server`* 앱에서 `Startup.ConfigureServices`에 프로필 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="7f48b-222">권한 부여 메커니즘 사용</span><span class="sxs-lookup"><span data-stu-id="7f48b-222">Use authorization mechanisms</span></span>

<span data-ttu-id="7f48b-223">이 시점에서는 *`Client`* 앱의 구성 요소 권한 부여 방식이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="7f48b-224">구성 요소의 모든 권한 부여 메커니즘에서 역할을 사용하여 사용자에게 권한을 부여할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="7f48b-225">[`AuthorizeView` 구성 요소](xref:blazor/security/index#authorizeview-component)(예: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="7f48b-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="7f48b-226">[`[Authorize]` 특성 지시문](xref:blazor/security/index#authorize-attribute)(<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)(예: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="7f48b-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="7f48b-227">[절차적 논리](xref:blazor/security/index#procedural-logic)(예: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="7f48b-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="7f48b-228">여러 역할 테스트가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="7f48b-229">*`Client`* 앱에서 `User.Identity.Name`에 사용자의 사용자 이름이 채워집니다. 이는 보통 사용자의 로그인 전자 메일 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-229">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="7f48b-230">사용자 지정 도메인을 사용하는 Azure App Service에서 호스트</span><span class="sxs-lookup"><span data-stu-id="7f48b-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="7f48b-231">다음 지침에서는 Identity 서버와 함께 호스티드 Blazor WebAssembly 앱을 사용자 지정 도메인을 사용하는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 배포하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="7f48b-232">이 호스팅 시나리오에서는 [Identity 서버의 토큰 서명 키](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) 및 브라우저에 대한 사이트의 HTTPS 보안 통신에 동일한 인증서를 사용하지 **않습니다**.</span><span class="sxs-lookup"><span data-stu-id="7f48b-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="7f48b-233">두 가지 해당 요구 사항에 서로 다른 인증서를 사용하는 것은 각 목적에 맞게 프라이빗 키를 격리하기 때문에 좋은 보안 사례입니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="7f48b-234">브라우저와 통신하기 위한 TLS 인증서는 Identity 서버의 토큰 서명에 영향을 주지 않고 독립적으로 관리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="7f48b-235">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 사용자 지정 도메인 바인딩을 위해 App Service 앱에 인증서를 제공하는 경우 Identity 서버는 토큰 서명을 위해 Azure Key Vault에서 동일한 인증서를 가져올 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="7f48b-236">실제 경로에서 동일한 TLS 인증서를 사용하도록 Identity 서버를 구성할 수 있지만 보안 인증서를 소스 제어에 배치하는 것은 **좋지 않은 사례이며 대부분의 시나리오에서 피해야 합니다**.</span><span class="sxs-lookup"><span data-stu-id="7f48b-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="7f48b-237">다음 지침에서 자체 서명된 인증서는 Identity 서버 토큰 서명을 위해서만 Azure Key Vault에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="7f48b-238">Identity 서버 구성은 앱의 `My` > `CurrentUser` 인증서 저장소를 통해 키 자격 증명 모음 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="7f48b-239">사용자 지정 도메인을 사용하는 HTTPS 트래픽에 사용되는 기타 인증서는 Identity 서버 서명 인증서와 별도로 생성 및 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="7f48b-240">사용자 지정 도메인 및 HTTPS를 사용하여 호스트하도록 앱, Azure App Service 및 Azure Key Vault를 구성하려면:</span><span class="sxs-lookup"><span data-stu-id="7f48b-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="7f48b-241">`Basic B1` 이상의 계획 수준을 사용하여 [App Service 요금제](/azure/app-service/overview-hosting-plans)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="7f48b-242">App Service에서는 사용자 지정 도메인을 사용하려면 `Basic B1` 이상 서비스 계층이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="7f48b-243">조직이 제어하는 사이트 FQDN(정규화된 도메인 이름)의 일반 이름(예: `www.contoso.com`)을 사용하여 사이트 보안 브라우저 통신(HTTPS 프로토콜)용 PFX 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="7f48b-244">다음을 사용하여 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-244">Create the certificate with:</span></span>
   * <span data-ttu-id="7f48b-245">키 사용</span><span class="sxs-lookup"><span data-stu-id="7f48b-245">Key uses</span></span>
     * <span data-ttu-id="7f48b-246">디지털 시그니처 유효성 검사(`digitalSignature`)</span><span class="sxs-lookup"><span data-stu-id="7f48b-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="7f48b-247">키 암호화(`keyEncipherment`)</span><span class="sxs-lookup"><span data-stu-id="7f48b-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="7f48b-248">확장 키 사용</span><span class="sxs-lookup"><span data-stu-id="7f48b-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="7f48b-249">클라이언트 인증(1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="7f48b-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="7f48b-250">서버 인증(1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="7f48b-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="7f48b-251">인증서를 만들려면 다음 접근 방식 중 하나를 사용하거나 다른 적합한 도구나 온라인 서비스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="7f48b-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7f48b-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="7f48b-253">Windows의 MakeCert</span><span class="sxs-lookup"><span data-stu-id="7f48b-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="7f48b-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="7f48b-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="7f48b-255">나중에 Azure Key Vault로 인증서를 가져오는 데 사용되는 암호를 기록해 둡니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="7f48b-256">Azure Key Vault 인증서에 관한 자세한 내용은 [Azure Key Vault: 인증서](/azure/key-vault/certificates/)를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="7f48b-257">새 Azure Key Vault를 만들거나 Azure 구독에서 기존 키 자격 증명 모음을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="7f48b-258">키 자격 증명 모음의 **인증서** 영역에서 PFX 사이트 인증서를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="7f48b-259">나중에 앱 구성에서 사용되는 인증서 지문을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="7f48b-260">Azure Key Vault에서 Identity 서버 토큰 서명을 위한 새로운 자체 서명된 인증서를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="7f48b-261">인증서에 **인증서 이름** 및 **주체** 를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="7f48b-262">**주체** 는 `CN={COMMON NAME}`으로 지정됩니다. 여기서 `{COMMON NAME}` 자리 표시자는 인증서의 일반 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="7f48b-263">일반 이름은 영숫자 문자열일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="7f48b-264">예를 들어 `CN=IdentityServerSigning`은 유효한 인증서 **주체** 입니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="7f48b-265">기본 **고급 정책 구성** 설정을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="7f48b-266">나중에 앱 구성에서 사용되는 인증서 지문을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="7f48b-267">Azure Portal에서 Azure App Service로 이동하고 다음 구성을 사용하여 새 App Service를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="7f48b-268">**게시** 를 `Code`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="7f48b-269">**런타임 스택** 을 앱 런타임으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="7f48b-270">**Sku 및 크기** 의 경우 App Service 계층이 `Basic B1` 이상인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="7f48b-271">App Service에서는 사용자 지정 도메인을 사용하려면 `Basic B1` 이상 서비스 계층이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="7f48b-272">Azure가 App Service를 만든 후 앱 **구성** 을 열고 이전에 기록된 인증서 지문을 지정하는 새 애플리케이션 설정을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="7f48b-273">앱 설정 키는 `WEBSITE_LOAD_CERTIFICATES`입니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="7f48b-274">다음 예제와 같이 앱 설정 값의 인증서 지문을 쉼표로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="7f48b-275">키: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="7f48b-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="7f48b-276">값: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="7f48b-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="7f48b-277">Azure Portal에서 앱 설정을 저장하는 작업은 2단계 프로세스입니다. `WEBSITE_LOAD_CERTIFICATES` 키-값 설정을 저장한 다음, 블레이드 위쪽에서 **저장** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="7f48b-278">앱의 **TLS/SSL 설정** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="7f48b-279">**프라이빗 키 인증서(.pfx)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="7f48b-280">**키 자격 증명 모음 인증서 가져오기** 프로세스를 두 번 사용하여 HTTPS 통신용 사이트 인증서 및 사이트의 자체 서명된 Identity 서버 토큰 서명 인증서를 둘 다 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="7f48b-281">**사용자 지정 도메인** 블레이드로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="7f48b-282">도메인 등록 기관의 웹 사이트에서 **IP 주소** 및 **사용자 지정 도메인 확인 ID** 를 사용하여 도메인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="7f48b-283">일반적인 도메인 구성에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="7f48b-284">`@`의 **호스트** 및 Azure Portal의 IP 주소 값이 포함된 **A 레코드**.</span><span class="sxs-lookup"><span data-stu-id="7f48b-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="7f48b-285">`asuid`의 **호스트** 및 Azure에서 생성하고 Azure Portal에서 제공하는 확인 ID 값이 포함된 **TXT 레코드**.</span><span class="sxs-lookup"><span data-stu-id="7f48b-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="7f48b-286">도메인 등록 기관의 웹 사이트에서 변경 내용을 올바르게 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="7f48b-287">일부 등록 기관 웹 사이트에서는 도메인 레코드를 저장하려면 2단계 프로세스가 필요합니다. 하나 이상의 레코드를 개별적으로 저장한 다음, 별도 단추를 사용하여 도메인 등록을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="7f48b-288">Azure Portal에서 **사용자 지정 도메인** 블레이드로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="7f48b-289">**사용자 지정 도메인 추가** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-289">Select **Add custom domain**.</span></span> <span data-ttu-id="7f48b-290">**A 레코드** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-290">Select the **A Record** option.</span></span> <span data-ttu-id="7f48b-291">도메인을 제공하고 **유효성 검사** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="7f48b-292">도메인 레코드가 올바르고 인터넷에서 전파되는 경우 포털에서 **사용자 지정 도메인 추가** 단추를 선택할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="7f48b-293">도메인 등록 변경이 도메인 등록 기관에서 처리된 후 인터넷 DNS(도메인 이름 서버)에서 전파되는 데 며칠이 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="7f48b-294">도메인 레코드가 영업일 기준 3일 이내에 업데이트되지 않는 경우 도메인 등록 기관을 통해 레코드가 올바르게 설정되었는지 확인하고 고객 지원에 문의하세요.</span><span class="sxs-lookup"><span data-stu-id="7f48b-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="7f48b-295">**사용자 지정 도메인** 블레이드에서 도메인의 **SSL 상태** 가 `Not Secure`로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="7f48b-296">**바인딩 추가** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-296">Select the **Add binding** link.</span></span> <span data-ttu-id="7f48b-297">사용자 지정 도메인 바인딩을 위해 키 자격 증명 모음에서 사이트 HTTPS 인증서를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="7f48b-298">Visual Studio에서 ‘서버’ 프로젝트의 앱 설정 파일(`appsettings.json` 또는 `appsettings.Production.json`)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="7f48b-299">Identity 서버 구성에서 다음 `Key` 섹션을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="7f48b-300">`Name` 키의 자체 서명된 인증서 **주체** 를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="7f48b-301">다음 예제에서는 키 자격 증명 모음에 할당된 인증서의 일반 이름이 `IdentityServerSigning`이며 이 이름에서 `CN=IdentityServerSigning`의 **주체** 가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="7f48b-302">Visual Studio에서 ‘서버’ 프로젝트의 Azure App Service [게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="7f48b-303">메뉴 모음에서 다음을 선택합니다. **빌드** > **게시** > **새로 만들기** > **Azure** > **Azure App Service**(Windows 또는 Linux).</span><span class="sxs-lookup"><span data-stu-id="7f48b-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="7f48b-304">Visual Studio가 Azure 구독에 연결된 경우 **리소스 종류** 별로 Azure 리소스의 **뷰** 를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="7f48b-305">**웹앱** 목록 내에서 탐색하여 앱의 App Service를 찾고 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="7f48b-306">**마침** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-306">Select **Finish**.</span></span>
1. <span data-ttu-id="7f48b-307">Visual Studio가 **게시** 창으로 돌아가면 키 자격 증명 모음 및 SQL Server 데이터베이스 서비스 종속성이 자동으로 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="7f48b-308">키 자격 증명 모음 서비스에는 기본 설정에 대한 구성 변경이 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="7f48b-309">테스트를 위해 기본적으로 Blazor 템플릿을 통해 구성되는 앱의 로컬 [SQLite](https://www.sqlite.org/index.html) 데이터베이스는 추가 구성 없이 앱과 함께 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="7f48b-310">프로덕션 환경에서 Identity 서버에 대해 다른 데이터베이스를 구성하는 작업은 이 문서의 범위를 벗어납니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="7f48b-311">자세한 내용은 다음 설명서 세트의 데이터베이스 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f48b-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="7f48b-312">App Service</span><span class="sxs-lookup"><span data-stu-id="7f48b-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="7f48b-313">Identity서버</span><span class="sxs-lookup"><span data-stu-id="7f48b-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="7f48b-314">창 위쪽에 있는 배포 프로필 이름에서 **편집** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="7f48b-315">대상 URL을 사이트의 사용자 지정 도메인 URL(예: `https://www.contoso.com`)로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="7f48b-316">설정을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-316">Save the settings.</span></span>
1. <span data-ttu-id="7f48b-317">앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-317">Publish the app.</span></span> <span data-ttu-id="7f48b-318">Visual Studio가 브라우저 창을 열고 해당 사용자 지정 도메인에서 사이트를 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="7f48b-319">Azure 설명서에는 A 레코드 대신 CNAME 레코드를 사용하는 방법에 관한 정보를 포함하여 App Service에서 TLS 바인딩과 함께 Azure 서비스 및 사용자 지정 도메인을 사용하는 방법에 관한 추가적인 세부 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="7f48b-320">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f48b-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="7f48b-321">App Service 설명서</span><span class="sxs-lookup"><span data-stu-id="7f48b-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="7f48b-322">자습서: Azure App Service에 기존 사용자 지정 DNS 이름 매핑</span><span class="sxs-lookup"><span data-stu-id="7f48b-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="7f48b-323">Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호</span><span class="sxs-lookup"><span data-stu-id="7f48b-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="7f48b-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7f48b-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="7f48b-325">Azure Portal에서 앱, 앱 구성 또는 Azure 서비스를 변경한 후 각 앱 테스트 실행을 위해 새로운 InPrivate 또는 incognito 브라우저 창을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="7f48b-326">이전 테스트 실행에서 느린 cookie로 인해 사이트의 구성이 올바르더라도 사이트를 테스트할 때 인증 또는 권한 부여에 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="7f48b-327">각 테스트 실행을 위해 새로운 InPrivate 또는 incognito 브라우저 창을 열도록 Visual Studio를 구성하는 방법에 관한 자세한 내용은 [Cookie 및 사이트 데이터](#cookies-and-site-data) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f48b-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="7f48b-328">Azure Portal에서 App Service 구성이 변경되면 업데이트는 일반적으로 신속하게 적용되지만 즉시 적용되지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="7f48b-329">경우에 따라 구성 변경 내용을 적용하려면 App Service가 다시 시작할 때까지 잠시 기다려야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="7f48b-330">인증서 로드 문제를 해결하는 경우 Azure Portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="7f48b-331">명령은 앱이 `My` > `CurrentUser` 인증서 저장소에서 액세스할 수 있는 인증서 목록을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="7f48b-332">출력에는 앱을 디버그할 때 유용한 인증서 주체 및 지문이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7f48b-333">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7f48b-333">Additional resources</span></span>

* [<span data-ttu-id="7f48b-334">Azure App Service에 배포</span><span class="sxs-lookup"><span data-stu-id="7f48b-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="7f48b-335">Key Vault에서 인증서 가져오기(Azure 설명서)</span><span class="sxs-lookup"><span data-stu-id="7f48b-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7f48b-336">보안 기본 클라이언트가 있는 앱의 인증되지 않거나 권한이 부여되지 않은 웹 API 요청</span><span class="sxs-lookup"><span data-stu-id="7f48b-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="7f48b-337"><xref:host-and-deploy/proxy-load-balancer>: 다음 사항에 대한 지침을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7f48b-337"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="7f48b-338">전달된 헤더 미들웨어를 사용하여 프록시 서버와 내부 네트워크에서 HTTPS 체계 정보 유지.</span><span class="sxs-lookup"><span data-stu-id="7f48b-338">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="7f48b-339">수동 체계 구성, 올바른 요청 라우팅에 대한 요청 경로 변경, Linux 및 비 IIS 역방향 프록시에 대한 요청 체계 전달을 포함한 추가 시나리오 및 사용 사례.</span><span class="sxs-lookup"><span data-stu-id="7f48b-339">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
