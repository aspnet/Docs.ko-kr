---
title: ASP.NET Core에서 역할 기반 권한 부여
author: rick-anderson
description: 권한 부여 특성에 역할을 전달 하 여 ASP.NET Core 컨트롤러 및 작업 액세스를 제한 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051163"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core에서 역할 기반 권한 부여

<a name="security-authorization-role-based"></a>

Id가 생성 되 면 하나 이상의 역할에 속할 수 있습니다. 예를 들어 Scott은 사용자 역할에만 속할 수 있는 Tracy는 관리자 및 사용자 역할에 속할 수 있습니다. 이러한 역할을 만들고 관리 하는 방법은 권한 부여 프로세스의 백업 저장소에 따라 달라 집니다. 역할은 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) 클래스의 [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) 메서드를 통해 개발자에 게 노출 됩니다.

## <a name="adding-role-checks"></a>역할 검사 추가

역할 기반 권한 부여 검사는 개발자가 코드 내에이를 포함 하 여 컨트롤러 &mdash; 또는 컨트롤러 내의 작업에 대 한 작업을 포함 하 여 요청 된 리소스에 액세스 하기 위해 현재 사용자가 멤버 여야 하는 역할을 지정 합니다.

예를 들어 다음 코드는 `AdministrationController` 역할의 멤버인 사용자에 대 한 작업에 대 한 액세스를 제한 합니다 `Administrator` .

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

쉼표로 구분 된 목록으로 여러 역할을 지정할 수 있습니다.

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

이 컨트롤러는 역할 또는 역할의 멤버인 사용자만 액세스할 수 있습니다 `HRManager` `Finance` .

여러 특성을 적용 하는 경우 액세스 하는 사용자는 지정 된 모든 역할의 멤버 여야 합니다. 다음 샘플을 사용 하려면 사용자가 및 역할의 멤버 여야 합니다 `PowerUser` `ControlPanelUser` .

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

작업 수준에서 추가 역할 권한 부여 특성을 적용 하 여 액세스를 추가로 제한할 수 있습니다.

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

위의 코드 조각에서 `Administrator` 역할 또는 역할의 멤버는 `PowerUser` 컨트롤러 및 작업에 액세스할 수 `SetTime` 있지만 역할의 멤버만이 `Administrator` 작업에 액세스할 수 있습니다 `ShutDown` .

또한 컨트롤러를 잠그고 개별 작업에 대 한 익명의 인증 되지 않은 액세스를 허용할 수 있습니다.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

페이지의 경우 Razor `AuthorizeAttribute` 다음 중 하나를 수행 하 여을 적용할 수 있습니다.

* [규칙](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)사용 또는
* 인스턴스에를 적용 `AuthorizeAttribute` 하는 `PageModel` 중입니다.

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> 을 포함 하 여 필터 특성은 `AuthorizeAttribute` PageModel에만 적용 될 수 있으며 특정 페이지 처리기 메서드에 적용할 수 없습니다.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>정책 기반 역할 검사

개발자가 시작 시 권한 부여 서비스 구성의 일부로 정책을 등록 하는 새 정책 구문을 사용 하 여 역할 요구 사항을 나타낼 수도 있습니다. 이는 일반적으로 `ConfigureServices()` *Startup.cs* 파일에서 발생 합니다.

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

특성의 속성을 사용 하 여 정책을 적용 합니다 `Policy` `AuthorizeAttribute` .

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

요구 사항에서 허용 되는 역할을 여러 개 지정 하려는 경우에는 메서드에 대 한 매개 변수로 지정할 수 있습니다 `RequireRole` .

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

이 예제에서는 또는 역할에 속한 사용자에 게 권한을 부여 `Administrator` `PowerUser` `BackupAdministrator` 합니다.

### <a name="add-role-services-to-no-locidentity"></a>역할 서비스 추가 Identity

역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

