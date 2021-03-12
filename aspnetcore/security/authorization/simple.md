---
title: ASP.NET Core의 단순 권한 부여
author: rick-anderson
description: 권한 부여 특성을 사용 하 여 ASP.NET Core 컨트롤러 및 작업에 대 한 액세스를 제한 하는 방법을 알아봅니다.
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
uid: security/authorization/simple
ms.openlocfilehash: eaf36822edc47f7238c356e167b05ac44f93175d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587569"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core의 단순 권한 부여

<a name="security-authorization-simple"></a>

ASP.NET Core의 권한 부여는 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 및 해당 하는 다양 한 매개 변수를 사용 하 여 제어 됩니다. 가장 간단한 형태의 `[Authorize]` 특성을 컨트롤러, 작업 또는 페이지에 적용 하면 Razor 해당 구성 요소에 대 한 액세스를 인증 된 사용자로 제한 합니다.

예를 들어 다음 코드는에 대 한 액세스를 `AccountController` 인증 된 사용자로 제한 합니다.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 `AuthorizeAttribute` 작업 자체에 특성을 적용 합니다.

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

이제 인증 된 사용자만 함수에 액세스할 수 있습니다 `Logout` .

또한 특성을 사용 `AllowAnonymous` 하 여 인증 되지 않은 사용자의 개별 작업에 대 한 액세스를 허용할 수 있습니다. 예를 들면 다음과 같습니다.

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

이렇게 하면 인증 된 사용자 `AccountController` `Login` 또는 인증 되지 않은/익명 상태에 관계 없이 모든 사용자가 액세스할 수 있는 동작을 제외 하 고는 인증 된 사용자만을 사용할 수 있습니다.

> [!WARNING]
> `[AllowAnonymous]` 모든 권한 부여 문을 무시 합니다. `[AllowAnonymous]`및 특성을 함께 사용할 경우 `[Authorize]` `[Authorize]` 특성이 무시 됩니다. 예를 들어 `[AllowAnonymous]` 컨트롤러 수준에서를 적용 하는 경우 `[Authorize]` 동일한 컨트롤러의 모든 특성 (또는 그 안에 있는 모든 작업)은 무시 됩니다.

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a>특성 및 Razor Pages 권한 부여

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>페이지 처리기에는를 적용할 수 ***없습니다*** Razor . 예를 들어,, `[Authorize]` `OnGet` `OnPost` 또는 다른 페이지 처리기에는를 적용할 수 없습니다. 다른 처리기에 대 한 권한 부여 요구 사항이 서로 다른 페이지에는 ASP.NET Core MVC 컨트롤러를 사용 하는 것이 좋습니다.

다음 두 가지 방법을 사용 하 여 페이지 처리기 메서드에 권한 부여를 적용할 수 있습니다 Razor .

* 다른 권한 부여를 필요로 하는 페이지 처리기에 대해 별도의 페이지를 사용 합니다. 공유 콘텐츠를 하나 이상의 [부분 뷰로](xref:mvc/views/partial)이동 합니다. 가능 하면 권장 되는 방법입니다.
* 공용 페이지를 공유 해야 하는 콘텐츠의 경우 권한 부여를 Iasyncpagefilter의 일부로 수행 하는 필터를 작성 합니다 [. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A). [Pagehandlerauth](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub 프로젝트는이 방법을 보여 줍니다.
  * [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) 은 권한 부여 필터를 구현 합니다.[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) 특성은 페이지 처리기에 적용 됩니다 `OnGet` .[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> [Pagehandlerauth](https://github.com/pranavkm/PageHandlerAuth) 샘플 방법은 다음을 수행 ***하지*** 않습니다.
> * 페이지, 페이지 모델 또는 전역에 적용 되는 권한 부여 특성으로 구성 합니다. 권한 부여 특성을 작성 하면 하나 이상의 `AuthorizeAttribute` 또는 `AuthorizeFilter` 인스턴스가 페이지에도 적용 될 때 인증 및 권한 부여가 여러 번 실행 됩니다.
> * ASP.NET Core 인증 및 권한 부여 시스템의 나머지 부분과 함께 작업 합니다. 응용 프로그램에서이 접근 방식을 사용 하는 것이 제대로 작동 하는지 확인 해야 합니다.

페이지 처리기에서를 지원할 계획이 없습니다 `AuthorizeAttribute` Razor . 
