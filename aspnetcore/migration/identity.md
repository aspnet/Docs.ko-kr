---
title: 인증 및 Identity ASP.NET Core로 마이그레이션
author: ardalis
description: ASP.NET MVC 프로젝트에서 ASP.NET Core MVC 프로젝트로 인증 및 id를 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060653"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a>인증 및 Identity ASP.NET Core로 마이그레이션

작성자: [Steve Smith](https://ardalis.com/)

이전 문서에서는 [ASP.NET mvc 프로젝트에서 ASP.NET CORE mvc로 구성을 마이그레이션](xref:migration/configuration)했습니다. 이 문서에서는 등록, 로그인 및 사용자 관리 기능을 마이그레이션합니다.

## <a name="configure-no-locidentity-and-membership"></a>구성 Identity 및 멤버 자격

ASP.NET MVC에서 인증 및 id 기능은 Identity *App_Start* 폴더에 있는 *Startup.Auth.cs* 및 *Identity Config.cs* 의 ASP.NET를 사용 하 여 구성 됩니다. ASP.NET Core MVC에서 이러한 기능은 *Startup.cs* 에서 구성 됩니다.

다음 NuGet 패키지를 설치합니다.

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

*Startup.cs* 에서 메서드를 업데이트 `Startup.ConfigureServices` 하 여 Entity Framework 및 서비스를 사용 합니다 Identity .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

이 시점에서 ASP.NET MVC 프로젝트에서 아직 마이그레이션하지 않은 두 가지 형식이 위의 코드에서 참조 되었습니다. `ApplicationDbContext` 및 `ApplicationUser` . ASP.NET Core 프로젝트에서 새 *모델* 폴더를 만들고 이러한 형식에 해당 하는 두 개의 클래스를 추가 합니다. */Models/ Identity Models.cs* 에서 이러한 클래스의 ASP.NET MVC 버전을 찾을 수 있지만 마이그레이션된 프로젝트에서 클래스 당 파일 하나를 사용 하는 것이 더 명확 하기 때문입니다.

*ApplicationUser.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

ASP.NET Core MVC 스타터 웹 프로젝트에는 사용자의 사용자 지정 또는가 포함 되어 있지 않습니다 `ApplicationDbContext` . 실제 앱을 마이그레이션할 때 앱이 사용 하는 다른 모델 클래스 뿐만 아니라 앱의 사용자 및 클래스의 사용자 지정 속성 및 메서드도 모두 마이그레이션해야 `DbContext` 합니다. 예를 들어에 `DbContext` 가 있으면 `DbSet<Album>` 클래스를 마이그레이션해야 `Album` 합니다.

이러한 파일이 준비 되 면 해당 문을 업데이트 하 여 *Startup.cs* 파일을 컴파일할 수 있습니다 `using` .

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

이제 앱이 인증 및 서비스를 지원할 준비가 되었습니다 Identity . 이러한 기능은 사용자에 게 노출 되어야 합니다.

## <a name="migrate-registration-and-login-logic"></a>등록 및 로그인 논리 마이그레이션

Identity앱에 대해 구성 된 서비스 및 Entity Framework 및 SQL Server를 사용 하 여 구성 된 데이터 액세스를 사용 하 여 등록에 대 한 지원을 추가 하 고 앱에 로그인 할 준비가 되었습니다. [마이그레이션 프로세스의 이전 단계에서는](xref:migration/mvc#migrate-the-layout-file) *_Layout* 의 *_LoginPartial* 에 대 한 참조를 주석으로 처리 했습니다. 이제 해당 코드로 돌아와서, 주석 처리를 제거 하 고, 필요한 컨트롤러 및 뷰를 추가 하 여 로그인 기능을 지원할 때입니다.

_Layout에서 줄의 주석 처리를 제거 `@Html.Partial` *합니다.*

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

이제 Razor *Views/Shared* 폴더에 *_LoginPartial* 라는 새 뷰를 추가 합니다.

다음 코드를 사용 하 여 _LoginPartial를 업데이트 *합니다* (모든 내용 바꾸기).

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

이 시점에서 브라우저에서 사이트를 새로 고칠 수 있습니다.

## <a name="summary"></a>요약

ASP.NET Core ASP.NET 기능에 대 한 변경 사항을 소개 Identity 합니다. 이 문서에서는 ASP.NET의 인증 및 사용자 관리 기능을 ASP.NET Core로 마이그레이션하는 방법에 대해 살펴보았습니다 Identity .
