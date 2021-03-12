---
title: ASP.NET Core 소개 Identity
author: rick-anderson
description: IdentityASP.NET Core 앱과 함께 사용 합니다. 암호 요구 사항 (RequireDigit, RequiredLength, RequiredUniqueChars 등)을 설정 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: 7141808b3190db2c882d562d753cd68fe3f985ff
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589740"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core 소개 Identity

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity:

* 는 UI (사용자 인터페이스) 로그인 기능을 지 원하는 API입니다.
* 사용자, 암호, 프로필 데이터, 역할, 클레임, 토큰, 전자 메일 확인 등을 관리 합니다.

사용자는에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 Identity 외부 로그인 공급자를 사용할 수 있습니다. 지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.

[!INCLUDE[](~/includes/requireAuth.md)]

[ Identity 소스 코드](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) 는 GitHub에서 사용할 수 있습니다. [스 캐 폴드 Identity ](xref:security/authentication/scaffold-identity) 및는 생성 된 파일을 확인 하 여와의 템플릿 상호 작용을 검토 Identity 합니다.

Identity 는 일반적으로 사용자 이름, 암호 및 프로필 데이터를 저장 하기 위해 SQL Server 데이터베이스를 사용 하 여 구성 됩니다. 또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.

이 항목에서는를 사용 하 여 사용자를 Identity 등록 하 고 로그인 하 고 로그 아웃 하는 방법을 알아봅니다. 참고: 템플릿은 사용자에 대해 사용자 이름 및 전자 메일을 동일 하 게 처리 합니다. 를 사용 하는 앱을 만드는 방법에 대 한 자세한 지침은 Identity [다음 단계](#next)를 참조 하세요.

[Microsoft id 플랫폼](/azure/active-directory/develop/) 은 다음과 같습니다.

* Azure Active Directory (Azure AD) 개발자 플랫폼의 진화
* 와 관련이 ASP.NET Core Identity 없습니다.

[!INCLUDE[](~/includes/IdentityServer4.md)]

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>인증을 사용 하 여 웹 앱 만들기

개별 사용자 계정을 사용 하 여 ASP.NET Core 웹 응용 프로그램 프로젝트를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일** > **새로 만들기** > **프로젝트** 를 선택 합니다.
* **새 ASP.NET Core 웹 애플리케이션** 을 선택합니다. 프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다. **확인** 을 클릭합니다.
* ASP.NET Core **웹 응용 프로그램** 을 선택한 다음 **인증 변경** 을 선택 합니다.
* **개별 사용자 계정을** 선택 하 고 **확인** 을 클릭 합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

이전 명령은 Razor SQLite를 사용 하 여 웹 앱을 만듭니다. LocalDB를 사용 하 여 웹 앱을 만들려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

생성 된 프로젝트는을 [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)제공 합니다. Identity Razor 클래스 라이브러리는 영역을 사용 하 여 끝점을 노출 `Identity` 합니다. 예를 들면 다음과 같습니다.

* /Identity/Account/Login
* /Identity/계정/로그 아웃
* /Identity/계정/관리

### <a name="apply-migrations"></a>마이그레이션 적용

마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.

`PM> Update-Database`

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

SQLite를 사용 하는 경우이 단계에서는 마이그레이션이 필요 하지 않습니다.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

LocalDB의 경우 다음 명령을 실행 합니다.

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>테스트 등록 및 로그인

앱을 실행 하 고 사용자를 등록 합니다. 화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Identity서비스 구성

서비스는에 추가 됩니다 `ConfigureServices` . 일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

위의 강조 표시 된 코드는 Identity 기본 옵션 값을 사용 하 여를 구성 합니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.

Identity 는를 호출 하 여 사용할 수 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 있습니다. `UseAuthentication` 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

위의 코드는 Identity 기본 옵션 값을 사용 하 여를 구성 합니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.

Identity 는 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 사용 하도록 설정 됩니다. `UseAuthentication` 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

템플릿에서 생성 된 앱은 [권한 부여](xref:security/authorization/secure-data)를 사용 하지 않습니다. `app.UseAuthorization` 는 앱이 권한 부여를 추가 하는 올바른 순서로 추가 되었는지 확인 하기 위해 포함 됩니다. `UseRouting`, `UseAuthentication` , `UseAuthorization` 및 `UseEndpoints` 는 앞의 코드에 표시 된 순서 대로 호출 해야 합니다.

및에 대 한 자세한 내용은 `IdentityOptions` `Startup` <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>스 캐 폴드 Register, Login, LogOut 및 RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

,, `Register` `Login` 및 파일을 추가 `LogOut` `RegisterConfirmation` 합니다. 이 섹션에 표시 된 코드를 생성 하려면 [ Razor 권한 부여 지침을 포함 하는 스 캐 폴드 id를 프로젝트에](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 추가 합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1** 이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다. 그렇지 않으면에 대해 올바른 네임 스페이스를 사용 합니다 `ApplicationDbContext` .

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다. PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.

스 캐 폴딩에 대 한 자세한 내용은 Identity [스 캐 폴드 identity to a Razor project to authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)항목을 참조 하세요.

---

### <a name="examine-register"></a>등록 검사

사용자가 페이지에서 **등록** 단추를 클릭 하면 `Register` `RegisterModel.OnPostAsync` 동작이 호출 됩니다. 사용자는 개체에 대해 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 를 사용 하 여 만들어집니다 `_userManager` .

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/main/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>로그인

다음과 같은 경우 로그인 양식이 표시 됩니다.

* **로그인** 링크가 선택 됩니다.
* 사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.

로그인 페이지의 폼이 제출 되 면 `OnPostAsync` 동작이 호출 됩니다. `PasswordSignInAsync` 는 개체에서 호출 됩니다 `_signInManager` .

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

권한 부여 결정을 내리는 방법에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authorization/introduction> .

### <a name="log-out"></a>로그아웃

**로그 아웃** 링크는 작업을 호출 합니다 `LogoutModel.OnPost` . 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

위의 코드에서는 `return RedirectToPage();` 브라우저가 새 요청을 수행 하 고 사용자에 대 한 id가 업데이트 되도록 코드를 리디렉션 해야 합니다.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 에 저장 된 사용자의 클레임을 지웁니다 cookie .

Post는 *Pages/Shared/_LoginPartial* 에서 지정 됩니다. cshtml:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>테스트할 Identity

기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다. 테스트 하려면 Identity 다음을 추가 합니다 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) .

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다. 로그인 페이지로 리디렉션됩니다.

### <a name="explore-identity"></a>탐험 Identity

자세히 살펴보기 Identity :

* [전체 id UI 원본 만들기](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.

## <a name="identity-components"></a>Identity 요소도

모든 Identity 종속 NuGet 패키지는 [ASP.NET Core 공유 프레임 워크](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)에 포함 되어 있습니다.

의 기본 패키지는 Identity [AspNetCore입니다. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/) 이 패키지에는에 대 한 핵심 인터페이스 집합이 포함 되어 ASP.NET Core Identity 있으며,에 포함 되어 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 있습니다.

## <a name="migrating-to-aspnet-core-identity"></a>로 마이그레이션 ASP.NET Core Identity

기존 저장소 마이그레이션에 대 한 자세한 내용 및 지침은 Identity [인증 및 Identity 마이그레이션 ](xref:migration/identity)을 참조 하세요.

## <a name="setting-password-strength"></a>암호 강도 설정

최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.

## <a name="adddefaultidentity-and-addidentity"></a>AddDefault Identity 및 AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 는 ASP.NET Core 2.1에서 도입 되었습니다. 호출은 `AddDefaultIdentity` 다음을 호출 하는 것과 비슷합니다.

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

자세한 내용은 [Adddefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.

## <a name="prevent-publish-of-static-identity-assets"></a>정적 자산 게시 방지 Identity

IdentityUI에 대 한 정적 자산 (스타일 시트 및 JavaScript 파일 Identity )을 웹 루트에 게시 하지 않으려면 다음 `ResolveStaticWebAssetsInputsDependsOn` 속성과 `RemoveIdentityAssets` 대상을 응용 프로그램의 프로젝트 파일에 추가 합니다.

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

* [ASP.NET Core Identity 소스 코드](https://github.com/dotnet/aspnetcore/tree/main/src/Identity)
* SQLite를 사용 하 여 구성 하는 방법에 대 한 자세한 내용은 [GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/5131) 를 참조 하세요 Identity .
* [Identity 구성](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity 는 ASP.NET Core 앱에 로그인 기능을 추가 하는 멤버 자격 시스템입니다. 사용자는에 저장 된 로그인 정보를 사용 하 여 계정을 만들거나 Identity 외부 로그인 공급자를 사용할 수 있습니다. 지원 되는 외부 로그인 공급자에는 [Facebook, Google, Microsoft 계정 및 Twitter](xref:security/authentication/social/index)가 포함 됩니다.

Identity SQL Server 데이터베이스를 사용 하 여 사용자 이름, 암호 및 프로필 데이터를 저장 하도록 구성할 수 있습니다. 또는 Azure Table Storage와 같은 또 다른 영구 저장소를 사용할 수 있습니다.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 항목에서는를 사용 하 여 사용자를 Identity 등록 하 고 로그인 하 고 로그 아웃 하는 방법을 알아봅니다. 를 사용 하는 앱을 만드는 방법에 대 한 자세한 내용은 Identity 이 문서의 끝에 있는 다음 단계 섹션을 참조 하세요.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefault Identity 및 AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 는 ASP.NET Core 2.1에서 도입 되었습니다. 호출은 `AddDefaultIdentity` 다음을 호출 하는 것과 비슷합니다.

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

자세한 내용은 [Adddefault Identity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 를 참조 하세요.

## <a name="create-a-web-app-with-authentication"></a>인증을 사용 하 여 웹 앱 만들기

개별 사용자 계정을 사용 하 여 ASP.NET Core 웹 응용 프로그램 프로젝트를 만듭니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일** > **새로 만들기** > **프로젝트** 를 선택 합니다.
* **새 ASP.NET Core 웹 애플리케이션** 을 선택합니다. 프로젝트의 이름을 **WebApp1** 프로젝트 다운로드와 동일한 네임 스페이스를 포함 합니다. **확인** 을 클릭합니다.
* ASP.NET Core **웹 응용 프로그램** 을 선택한 다음 **인증 변경** 을 선택 합니다.
* **개별 사용자 계정을** 선택 하 고 **확인** 을 클릭 합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

생성 된 프로젝트는을 [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)제공 합니다. Identity Razor 클래스 라이브러리는 영역을 사용 하 여 끝점을 노출 `Identity` 합니다. 예를 들면 다음과 같습니다.

* /Identity/Account/Login
* /Identity/계정/로그 아웃
* /Identity/계정/관리

### <a name="apply-migrations"></a>마이그레이션 적용

마이그레이션을 적용 하 여 데이터베이스를 초기화 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

패키지 관리자 콘솔 (PMC)에서 다음 명령을 실행 합니다.

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>테스트 등록 및 로그인

앱을 실행 하 고 사용자를 등록 합니다. 화면 크기에 따라, 탐색 토글 단추를 선택 하 여 **등록** 및 **로그인** 링크를 확인 해야 할 수도 있습니다.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Identity서비스 구성

서비스는에 추가 됩니다 `ConfigureServices` . 일반적인 패턴은 모든 `Add{Service}` 메서드를 호출한 후 모든 `services.Configure{Service}` 메서드를 호출하는 것입니다.

위의 코드는 Identity 기본 옵션 값을 사용 하 여를 구성 합니다. 서비스는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱에서 사용할 수 있게 됩니다.

Identity 는 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)을 호출 하 여 사용 하도록 설정 됩니다. `UseAuthentication` 인증 [미들웨어](xref:fundamentals/middleware/index) 를 요청 파이프라인에 추가 합니다.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

자세한 내용은 [ Identity 옵션 클래스](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) 및 [응용 프로그램 시작](xref:fundamentals/startup)을 참조 하세요.

## <a name="scaffold-register-login-and-logout"></a>스 캐 폴드 Register, Login 및 LogOut

이 섹션에 표시 된 코드를 생성 하려면 [ Razor 권한 부여 지침을 포함 하는 스 캐 폴드 id를 프로젝트에](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 추가 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Register, Login 및 LogOut 파일을 추가 합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

**WebApp1** 이름으로 프로젝트를 만든 경우 다음 명령을 실행 합니다. 그렇지 않으면에 대해 올바른 네임 스페이스를 사용 합니다 `ApplicationDbContext` .

SQLite를 사용 하는 경우 `--useSqLite` 다음을 지정 해야 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout" --useSqLite
```

SQL Express를 사용 하 여 다음 명령을 사용 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell은 세미콜론을 명령 구분 기호로 사용 합니다. PowerShell을 사용 하는 경우 파일 목록에서 세미콜론을 이스케이프 하거나 앞의 예제와 같이 큰따옴표 안에 파일 목록을 넣습니다.

---

### <a name="examine-register"></a>등록 검사

사용자가 **등록** 링크를 클릭 하면 `RegisterModel.OnPostAsync` 작업이 호출 됩니다. 사용자는 개체에 대해 [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 를 사용 하 여 만들어집니다 `_userManager` .

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

사용자가 성공적으로 만들어진 경우에는에 대 한 호출을 통해 사용자가 로그인 됩니다 `_signInManager.SignInAsync` .

**참고:** 등록 시 즉각적인 로그인을 방지 하는 단계는 [계정 확인](xref:security/authentication/accconfirm#prevent-login-at-registration) 을 참조 하세요.

### <a name="log-in"></a>로그인

다음과 같은 경우 로그인 양식이 표시 됩니다.

* **로그인** 링크가 선택 됩니다.
* 사용자가 액세스 권한이 없는 제한 된 페이지에 액세스 **하거나** 시스템이 인증 되지 않은 경우에 액세스 하려고 합니다.

로그인 페이지의 폼이 제출 되 면 `OnPostAsync` 동작이 호출 됩니다. `PasswordSignInAsync` 는 개체에서 호출 됩니다 `_signInManager` .

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

권한 부여 결정을 내리는 방법에 대 한 자세한 내용은을 참조 하십시오 <xref:security/authorization/introduction> .

### <a name="log-out"></a>로그아웃

**로그 아웃** 링크는 작업을 호출 합니다 `LogoutModel.OnPost` . 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 에 저장 된 사용자의 클레임을 지웁니다 cookie .

Post는 *Pages/Shared/_LoginPartial* 에서 지정 됩니다. cshtml:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>테스트할 Identity

기본 웹 프로젝트 템플릿을 사용 하면 홈 페이지에 익명으로 액세스할 수 있습니다. 테스트 하려면 Identity [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 개인 정보 페이지에를 추가 합니다.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

로그인 한 경우 로그 아웃 합니다. 앱을 실행 하 고 **개인 정보** 링크를 선택 합니다. 로그인 페이지로 리디렉션됩니다.

### <a name="explore-identity"></a>탐험 Identity

자세히 살펴보기 Identity :

* [전체 id UI 원본 만들기](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 각 페이지의 소스를 검사 하 고 디버거를 단계별로 실행 합니다.

## <a name="identity-components"></a>Identity 요소도

모든 Identity 종속 NuGet 패키지는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)에 포함 되어 있습니다.

의 기본 패키지는 Identity [AspNetCore입니다. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/) 이 패키지에는에 대 한 핵심 인터페이스 집합이 포함 되어 ASP.NET Core Identity 있으며,에 포함 되어 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 있습니다.

## <a name="migrating-to-aspnet-core-identity"></a>로 마이그레이션 ASP.NET Core Identity

기존 저장소 마이그레이션에 대 한 자세한 내용 및 지침은 Identity [인증 및 Identity 마이그레이션 ](xref:migration/identity)을 참조 하세요.

## <a name="setting-password-strength"></a>암호 강도 설정

최소 암호 요구 사항을 설정 하는 예제는 [구성](#pw) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* SQLite를 사용 하 여 구성 하는 방법에 대 한 자세한 내용은 [GitHub 문제](https://github.com/dotnet/AspNetCore.Docs/issues/5131) 를 참조 하세요 Identity .
* [Identity 구성](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
