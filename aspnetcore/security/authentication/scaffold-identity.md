---
title: IdentityASP.NET Core 프로젝트의 스 캐 폴드
author: rick-anderson
description: ASP.NET Core 프로젝트에서 스 캐 폴드 하는 방법에 대해 알아봅니다 Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053321"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>IdentityASP.NET Core 프로젝트의 스 캐 폴드

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core은 [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)제공 됩니다. 를 포함 하는 응용 프로그램 Identity 은 스 캐 폴더를 적용 하 여 Identity Razor 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다. 코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다. 예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다. 생성된 코드는 Identity RCL의 동일한 코드보다 우선합니다. 기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 Identity Ui 원본 만들기](#full)섹션을 참조 하세요.

인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl 패키지를 추가할 수 있습니다 Identity . 선택한 Identity 코드의 옵션이 생성되어야 합니다.

스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다. 이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.

파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다. 스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다 Identity .

서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 경우에 필요 Identity 합니다. 스 캐 폴딩 할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다 Identity . 이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다. 예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.

Identity기존 개별 계정이 있는 프로젝트에 새 데이터 컨텍스트를 사용 하 여 스 캐 폴딩 할 때:

* 에서 `Startup.ConfigureServices` 에 대 한 호출을 제거 합니다.
  * `AddDbContext`
  * `AddDefaultIdentity`

예를 들어 `AddDbContext` 및 `AddDefaultIdentity` 는 다음 코드에서 주석 처리 됩니다.

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

앞 코드는 *영역/ Identity / Identity HostingStartup.cs* 에서 중복 된 코드를 주석으로 처리 합니다.

일반적으로 개별 계정을 사용 하 여 만든 앱은 새 데이터 컨텍스트를 **만들지 않아야 합니다** .

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Identity빈 프로젝트로 스 캐 폴드

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Identity Razor 기존 권한 부여 없이 프로젝트에 스 캐 폴드

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity는 _Areas/HostingStartup.cs *에서 구성 됩니다 Identity / Identity . 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>마이그레이션, UseAuthentication 및 레이아웃

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>인증 사용

다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>레이아웃 변경

선택 사항: 로그인 부분 ( `_LoginPartial` )을 레이아웃 파일에 추가 합니다.

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Identity Razor 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

일부 Identity 옵션은 *영역/ Identity / Identity HostingStartup.cs* 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Identity기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

선택 사항: `_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* *Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*

Identity는 *영역/ Identity / Identity HostingStartup.cs* 에서 구성 됩니다. 자세한 내용은 IHostingStartup를 참조 하세요.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

다음과 유사한 코드를 사용 하 여 클래스를 업데이트 합니다 `Startup` .

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Identity권한 부여를 사용 하 여 MVC 프로젝트로 스 캐 폴드

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Identity Blazor Server 기존 권한 부여 없이 프로젝트에 스 캐 폴드

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity는 *영역/ Identity / Identity HostingStartup.cs* 에서 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

### <a name="migrations"></a>마이그레이션

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>앱에 XSRF 토큰 전달

토큰은 구성 요소에 전달할 수 있습니다.

* 인증 토큰이 프로 비전 되 고 인증에 저장 될 때 cookie 구성 요소에 전달 될 수 있습니다.
* Razor 구성 요소 `HttpContext` 는 직접 사용할 수 없으므로의 로그 아웃 끝점에 게시 하기 위해 [XSRF (요청 방지 위조) 토큰](xref:security/anti-request-forgery) 을 얻을 수 있는 방법이 없습니다 Identity `/Identity/Account/Logout` . XSRF 토큰을 구성 요소에 전달할 수 있습니다.

자세한 내용은 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>를 참조하세요.

*Pages/_Host* 파일에서 및 클래스에 토큰을 추가한 후에 토큰을 설정 합니다 `InitialApplicationState` `TokenProvider` .

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

`App`를 할당 하기 위해 구성 요소 ( *응용 프로그램 razor* )를 업데이트 합니다 `InitialState.XsrfToken` .

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

항목에서 설명 하는 `TokenProvider` 서비스는 `LoginDisplay` 다음 [레이아웃 및 인증 흐름 변경](#layout-and-authentication-flow-changes) 섹션의 구성 요소에서 사용 됩니다.

### <a name="enable-authentication"></a>인증 사용

클래스에서 `Startup` :

* Razor에서 Pages services가 추가 되었는지 확인 `Startup.ConfigureServices` 합니다.
* [Tokenprovider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)를 사용 하는 경우 서비스를 등록 합니다.
* `UseDatabaseErrorPage` `Startup.Configure` 개발 환경에 대 한의 응용 프로그램 작성기에서를 호출 합니다.
* `UseAuthentication`및 이후를 호출 `UseAuthorization` `UseRouting` 합니다.
* 페이지에 대 한 끝점을 추가 Razor 합니다.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>레이아웃 및 인증 흐름 변경

`RedirectToLogin`프로젝트 루트의 앱 *공유* 폴더에 구성 요소 ( *redirecttologin. razor* )를 추가 합니다.

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`앱의 *공유* 폴더에 구성 요소 ( *LoginDisplay* )를 추가 합니다. [Tokenprovider 서비스](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) 는 Identity 의 로그 아웃 끝점에 게시 하는 HTML 폼에 XSRF 토큰을 제공 합니다.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

`MainLayout`구성 요소 ( *Shared/mainlayout. razor* )에서 `LoginDisplay` 구성 요소를 상위 행 요소 내용에 추가 합니다 `<div>` .

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>스타일 인증 끝점

Blazor Server에서는 페이지 Razor 페이지를 사용 하므로 Identity 방문자가 Identity 페이지와 구성 요소 간을 탐색할 때 UI의 스타일이 변경 됩니다. Incongruous 스타일을 해결 하는 두 가지 옵션이 있습니다.

#### <a name="build-no-locidentity-components"></a>빌드 Identity 구성 요소

페이지 대신 구성 요소를 사용 하는 방법은 Identity 구성 요소를 빌드하는 것입니다 Identity . `SignInManager`및 `UserManager` 는 구성 요소에서 지원 되지 않으므로 Razor 앱에서 API 끝점을 사용 Blazor Server 하 여 사용자 계정 작업을 처리 합니다.

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>앱 스타일을 사용 하 여 사용자 지정 레이아웃 사용 Blazor

Identity페이지 레이아웃 및 스타일을 수정 하 여 기본 테마를 사용 하는 페이지를 만들 수 있습니다 Blazor .

> [!NOTE]
> 이 섹션의 예제는 단순히 사용자 지정을 위한 출발점입니다. 최상의 사용자 환경을 위해 추가 작업이 필요할 수 있습니다.

새 `NavMenu_IdentityLayout` 구성 요소 ( *공유/NavMenu_ Identity 레이아웃. razor* )를 만듭니다. 구성 요소의 태그와 코드에 대해 앱 `NavMenu` 구성 요소의 동일한 콘텐츠 ( *Shared/NavMenu* )를 사용 합니다. `NavLink` `RedirectToLogin` 구성 요소의 자동 리디렉션이 인증 또는 권한 부여가 필요한 구성 요소에 대해 실패 하므로 익명으로 연결할 수 없는 구성 요소에 대 한 모든를 제거 합니다.

*Pages/Shared/Layout. cshtml* 파일에서 다음과 같이 변경 합니다.

* Razor태그 도우미와 앱의 구성 요소를 *공유* 폴더에 사용 하려면 파일의 맨 위에 지시문을 추가 합니다.

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  를 `{APPLICATION ASSEMBLY}` 앱의 어셈블리 이름으로 바꿉니다.

* `<base>`콘텐츠에 태그 및 Blazor 스타일 시트를 추가 `<link>` 합니다 `<head>` .

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* 태그의 내용을 `<body>` 다음과 같이 변경 합니다.

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Identity Blazor Server 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

일부 Identity 옵션은 *영역/ Identity / Identity HostingStartup.cs* 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>전체 Identity UI 원본 만들기

UI에 대 한 모든 권한을 유지 하려면 Identity 스 캐 폴더을 실행 하 Identity 고 **모든 파일 재정의** 를 선택 합니다.

다음 강조 표시 된 코드는 Identity Identity ASP.NET Core 2.1 웹 앱에서 기본 UI를 바꿀 변경 내용을 보여 줍니다. 이 작업을 수행 하 여 UI에 대 한 모든 권한을 부여할 수 있습니다 Identity .

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

기본값은 Identity 다음 코드에서 대체 됩니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

구현을 등록 `IEmailSender` 합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>암호 구성

<xref:Microsoft.AspNetCore.Identity.PasswordOptions>이에서 구성 된 경우 `Startup.ConfigureServices` 스 캐 폴드 pages의 속성에 대 한 [ `[StringLength]` 특성](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) 구성이 필요할 수 있습니다 `Password` Identity . `InputModel``Password`속성은 다음 파일에 있습니다.

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>페이지 사용 안 함

이 섹션에서는 등록 페이지를 사용 하지 않도록 설정 하는 방법을 보여 주지만,이 방법을 사용 하 여 페이지를 비활성화할 수 있습니다.

사용자 등록을 사용 하지 않도록 설정 하려면:

* 스 캐 폴드 Identity . Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다. 다음은 그 예입니다.

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 사용자가이 끝점에서 등록할 수 없도록 *영역/ Identity /Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 이전 변경 내용과 일치 하도록 *영역/ Identity /Pages/Account/Register.cshtml* 를 업데이트 합니다.

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *영역/ Identity /Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* *영역/ Identity /Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.

  * Cshtml 파일에서 코드 및 링크를 제거 합니다.
  * 에서 확인 코드를 제거 합니다 `PageModel` .

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>다른 앱을 사용 하 여 사용자 추가

웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다. 사용자를 추가 하는 옵션은 다음과 같습니다.

* 전용 관리 웹 앱입니다.
* 콘솔 앱입니다.

다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.

* 사용자 목록을 메모리로 읽습니다.
* 각 사용자에 대해 강력한 고유 암호가 생성 됩니다.
* 사용자가 데이터베이스에 추가 됩니다 Identity .
* 사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>정적 자산 게시 방지 Identity

정적 Identity 자산을 웹 루트에 게시 하지 않으려면를 참조 하십시오 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core 2.1 이상으로 인증 코드 변경](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 이상에서는 [ASP.NET Core Identity](xref:security/authentication/identity) [ Razor 클래스 라이브러리로](xref:razor-pages/ui-class)를 제공 합니다. 를 포함 하는 응용 프로그램 Identity 은 스 캐 폴더를 적용 하 여 Identity Razor 클래스 라이브러리 (rcl)에 포함 된 소스 코드를 선택적으로 추가할 수 있습니다. 코드를 수정하고 동작을 변경할 수 있도록 소스 코드를 생성할 수 있습니다. 예를 들어 등록에 사용된 코드를 생성하도록 스캐폴더를 지정할 수 있습니다. 생성된 코드는 Identity RCL의 동일한 코드보다 우선합니다. 기본 RCL을 사용 하지 않고 UI에 대 한 모든 권한을 얻으려면 [전체 ID UI 원본 만들기](#full)섹션을 참조 하세요.

인증을 포함 **하지** 않는 응용 프로그램은 스 캐 폴더를 적용 하 여 rcl 패키지를 추가할 수 있습니다 Identity . 선택한 Identity 코드의 옵션이 생성되어야 합니다.

스 캐 폴더는 대부분의 필요한 코드를 생성 하지만 프로세스를 완료 하려면 프로젝트를 업데이트 해야 합니다. 이 문서에서는 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 단계를 설명 합니다.

스 캐 폴더를 Identity 실행 하면 프로젝트 디렉터리에 *ScaffoldingReadme.txt* 파일이 만들어집니다. *ScaffoldingReadme.txt* 파일은 Identity 스 캐 폴딩 업데이트를 완료 하는 데 필요한 사항에 대 한 일반적인 지침을 포함 합니다. 이 문서에는 *ScaffoldingReadme.txt* 파일 보다 더 완전 한 지침이 포함 되어 있습니다.

파일 차이를 표시 하는 소스 제어 시스템을 사용 하 여 변경 내용을 되돌릴 수 있도록 하는 것이 좋습니다. 스 캐 폴더를 실행 한 후에 변경 내용을 검사 합니다 Identity .

> [!NOTE]
> 서비스는 [두 가지 요소 인증](xref:security/authentication/identity-enable-qrcodes), [계정 확인 및 암호 복구](xref:security/authentication/accconfirm)및 기타 보안 기능을 사용 하는 경우에 필요 Identity 합니다. 스 캐 폴딩 할 때 서비스 또는 서비스 스텁이 생성 되지 않습니다 Identity . 이러한 기능을 사용 하도록 설정 하는 서비스는 수동으로 추가 해야 합니다. 예를 들어 [전자 메일 확인 필요](xref:security/authentication/accconfirm#require-email-confirmation)를 참조 하세요.

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Identity빈 프로젝트로 스 캐 폴드

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

클래스에 다음의 강조 표시 된 호출을 추가 합니다 `Startup` .

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Identity Razor 기존 권한 부여 없이 프로젝트에 스 캐 폴드

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity는 *영역/ Identity / Identity HostingStartup.cs* 에서 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>마이그레이션, UseAuthentication 및 레이아웃

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>인증 사용

`Configure`클래스의 메서드에서 다음 `Startup` 이후에 [useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 을 호출 합니다 `UseStaticFiles` .

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>레이아웃 변경

선택 사항: 로그인 부분 ( `_LoginPartial` )을 레이아웃 파일에 추가 합니다.

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Identity Razor 권한 부여를 사용 하 여 프로젝트로 스 캐 폴드

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

일부 Identity 옵션은 *영역/ Identity / Identity HostingStartup.cs* 구성 됩니다. 자세한 내용은 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)를 참조 하세요.

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Identity기존 권한 부여 없이 MVC 프로젝트에 스 캐 폴드

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

선택 사항: `_LoginPartial` *Views/Shared/_Layout cshtml* 파일에 로그인 부분 ()을 추가 합니다.

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* *Pages/shared/_LoginPartial cshtml* 파일을 *Views/shared/_LoginPartial로 이동 합니다.*

Identity는 *영역/ Identity / Identity HostingStartup.cs* 에서 구성 됩니다. 자세한 내용은 IHostingStartup를 참조 하세요.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

다음 이후에 [Useauthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 호출 `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Identity권한 부여를 사용 하 여 MVC 프로젝트로 스 캐 폴드

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

*Pages/Shared* 폴더와 해당 폴더의 파일을 삭제 합니다.

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>전체 Identity UI 원본 만들기

UI에 대 한 모든 권한을 유지 하려면 Identity 스 캐 폴더을 실행 하 Identity 고 **모든 파일 재정의** 를 선택 합니다.

다음 강조 표시 된 코드는 Identity Identity ASP.NET Core 2.1 웹 앱에서 기본 UI를 바꿀 변경 내용을 보여 줍니다. 이 작업을 수행 하 여 UI에 대 한 모든 권한을 부여할 수 있습니다 Identity .

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

기본값은 Identity 다음 코드에서 대체 됩니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

다음 코드에서는 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)및 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)를 설정 합니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

구현을 등록 `IEmailSender` 합니다. 예를 들면 다음과 같습니다.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>암호 구성

<xref:Microsoft.AspNetCore.Identity.PasswordOptions>이에서 구성 된 경우 `Startup.ConfigureServices` 스 캐 폴드 pages의 속성에 대 한 [ `[StringLength]` 특성](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) 구성이 필요할 수 있습니다 `Password` Identity . `InputModel``Password`속성은 다음 파일에 있습니다.

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>등록 페이지 사용 안 함

사용자 등록을 사용 하지 않도록 설정 하려면:

* 스 캐 폴드 Identity . Account. Register, Account. Login 및 Account. RegisterConfirmation 같이 포함 됩니다. 다음은 그 예입니다.

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 사용자가이 끝점에서 등록할 수 없도록 *영역/ Identity /Pages/Account/Register.cshtml.cs* 를 업데이트 합니다.

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 이전 변경 내용과 일치 하도록 *영역/ Identity /Pages/Account/Register.cshtml* 를 업데이트 합니다.

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *영역/ Identity /Pages/Account/Login.cshtml* 에서 등록 링크를 주석으로 처리 하거나 제거 합니다.

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* *영역/ Identity /Pages/Account/RegisterConfirmation* 페이지를 업데이트 합니다.

  * Cshtml 파일에서 코드 및 링크를 제거 합니다.
  * 에서 확인 코드를 제거 합니다 `PageModel` .

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>다른 앱을 사용 하 여 사용자 추가

웹 앱 외부에 사용자를 추가 하는 메커니즘을 제공 합니다. 사용자를 추가 하는 옵션은 다음과 같습니다.

* 전용 관리 웹 앱입니다.
* 콘솔 앱입니다.

다음 코드에서는 사용자를 추가 하는 한 가지 방법을 간략하게 설명 합니다.

* 사용자 목록을 메모리로 읽습니다.
* 각 사용자에 대해 강력한 고유 암호가 생성 됩니다.
* 사용자가 데이터베이스에 추가 됩니다 Identity .
* 사용자에 게 알림이 표시 되 고 암호를 변경 하 라는 메시지가 표시 됩니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

다음 코드에서는 사용자를 추가 하는 방법을 설명 합니다.

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

프로덕션 시나리오의 경우 유사한 접근 방식을 사용할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [ASP.NET Core 2.1 이상으로 인증 코드 변경](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
