---
title: '인증 및 :::no-loc(Identity)::: ASP.NET Core로 마이그레이션'
author: ardalis
description: ASP.NET MVC 프로젝트에서 ASP.NET Core MVC 프로젝트로 인증 및 id를 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 3/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060653"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a><span data-ttu-id="39b1a-103">인증 및 :::no-loc(Identity)::: ASP.NET Core로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="39b1a-103">Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core</span></span>

<span data-ttu-id="39b1a-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="39b1a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="39b1a-105">이전 문서에서는 [ASP.NET mvc 프로젝트에서 ASP.NET CORE mvc로 구성을 마이그레이션](xref:migration/configuration)했습니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="39b1a-106">이 문서에서는 등록, 로그인 및 사용자 관리 기능을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-no-locidentity-and-membership"></a><span data-ttu-id="39b1a-107">구성 :::no-loc(Identity)::: 및 멤버 자격</span><span class="sxs-lookup"><span data-stu-id="39b1a-107">Configure :::no-loc(Identity)::: and Membership</span></span>

<span data-ttu-id="39b1a-108">ASP.NET MVC에서 인증 및 id 기능은 :::no-loc(Identity)::: *App_Start* 폴더에 있는 *Startup.Auth.cs* 및 *:::no-loc(Identity)::: Config.cs* 의 ASP.NET를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET :::no-loc(Identity)::: in *Startup.Auth.cs* and *:::no-loc(Identity):::Config.cs* , located in the *App_Start* folder.</span></span> <span data-ttu-id="39b1a-109">ASP.NET Core MVC에서 이러한 기능은 *Startup.cs* 에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-109">In ASP.NET Core MVC, these features are configured in *Startup.cs* .</span></span>

<span data-ttu-id="39b1a-110">다음 NuGet 패키지를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-110">Install the following NuGet packages:</span></span>

* `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s`
* `Microsoft.EntityFrameworkCore.SqlServer`

<span data-ttu-id="39b1a-111">*Startup.cs* 에서 메서드를 업데이트 `Startup.ConfigureServices` 하 여 Entity Framework 및 서비스를 사용 합니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="39b1a-111">In *Startup.cs* , update the `Startup.ConfigureServices` method to use Entity Framework and :::no-loc(Identity)::: services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="39b1a-112">이 시점에서 ASP.NET MVC 프로젝트에서 아직 마이그레이션하지 않은 두 가지 형식이 위의 코드에서 참조 되었습니다. `ApplicationDbContext` 및 `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="39b1a-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="39b1a-113">ASP.NET Core 프로젝트에서 새 *모델* 폴더를 만들고 이러한 형식에 해당 하는 두 개의 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="39b1a-114">*/Models/ :::no-loc(Identity)::: Models.cs* 에서 이러한 클래스의 ASP.NET MVC 버전을 찾을 수 있지만 마이그레이션된 프로젝트에서 클래스 당 파일 하나를 사용 하는 것이 더 명확 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-114">You will find the ASP.NET MVC versions of these classes in */Models/:::no-loc(Identity):::Models.cs* , but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="39b1a-115">*ApplicationUser.cs* :</span><span class="sxs-lookup"><span data-stu-id="39b1a-115">*ApplicationUser.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : :::no-loc(Identity):::User
  {
  }
}
```

<span data-ttu-id="39b1a-116">*ApplicationDbContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="39b1a-116">*ApplicationDbContext.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
            // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="39b1a-117">ASP.NET Core MVC 스타터 웹 프로젝트에는 사용자의 사용자 지정 또는가 포함 되어 있지 않습니다 `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="39b1a-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="39b1a-118">실제 앱을 마이그레이션할 때 앱이 사용 하는 다른 모델 클래스 뿐만 아니라 앱의 사용자 및 클래스의 사용자 지정 속성 및 메서드도 모두 마이그레이션해야 `DbContext` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="39b1a-119">예를 들어에 `DbContext` 가 있으면 `DbSet<Album>` 클래스를 마이그레이션해야 `Album` 합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="39b1a-120">이러한 파일이 준비 되 면 해당 문을 업데이트 하 여 *Startup.cs* 파일을 컴파일할 수 있습니다 `using` .</span><span class="sxs-lookup"><span data-stu-id="39b1a-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.:::no-loc(Identity):::;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="39b1a-121">이제 앱이 인증 및 서비스를 지원할 준비가 되었습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="39b1a-121">Our app is now ready to support authentication and :::no-loc(Identity)::: services.</span></span> <span data-ttu-id="39b1a-122">이러한 기능은 사용자에 게 노출 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="39b1a-123">등록 및 로그인 논리 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="39b1a-123">Migrate registration and login logic</span></span>

<span data-ttu-id="39b1a-124">:::no-loc(Identity):::앱에 대해 구성 된 서비스 및 Entity Framework 및 SQL Server를 사용 하 여 구성 된 데이터 액세스를 사용 하 여 등록에 대 한 지원을 추가 하 고 앱에 로그인 할 준비가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-124">With :::no-loc(Identity)::: services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="39b1a-125">[마이그레이션 프로세스의 이전 단계에서는](xref:migration/mvc#migrate-the-layout-file) *_Layout* 의 *_LoginPartial* 에 대 한 참조를 주석으로 처리 했습니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml* .</span></span> <span data-ttu-id="39b1a-126">이제 해당 코드로 돌아와서, 주석 처리를 제거 하 고, 필요한 컨트롤러 및 뷰를 추가 하 여 로그인 기능을 지원할 때입니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="39b1a-127">_Layout에서 줄의 주석 처리를 제거 `@Html.Partial` *합니다.*</span><span class="sxs-lookup"><span data-stu-id="39b1a-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml* :</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="39b1a-128">이제 :::no-loc(Razor)::: *Views/Shared* 폴더에 *_LoginPartial* 라는 새 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-128">Now, add a new :::no-loc(Razor)::: view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="39b1a-129">다음 코드를 사용 하 여 _LoginPartial를 업데이트 *합니다* (모든 내용 바꾸기).</span><span class="sxs-lookup"><span data-stu-id="39b1a-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="39b1a-130">이 시점에서 브라우저에서 사이트를 새로 고칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="39b1a-131">요약</span><span class="sxs-lookup"><span data-stu-id="39b1a-131">Summary</span></span>

<span data-ttu-id="39b1a-132">ASP.NET Core ASP.NET 기능에 대 한 변경 사항을 소개 :::no-loc(Identity)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="39b1a-132">ASP.NET Core introduces changes to the ASP.NET :::no-loc(Identity)::: features.</span></span> <span data-ttu-id="39b1a-133">이 문서에서는 ASP.NET의 인증 및 사용자 관리 기능을 ASP.NET Core로 마이그레이션하는 방법에 대해 살펴보았습니다 :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="39b1a-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET :::no-loc(Identity)::: to ASP.NET Core.</span></span>
