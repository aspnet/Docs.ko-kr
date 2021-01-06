---
title: ASP.NET Core에서 HttpContext에 액세스
author: coderandhiker
description: ASP.NET Core에서 HttpContext에 액세스하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
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
uid: fundamentals/httpcontext
ms.openlocfilehash: d5ada72bb03cd74cf77c31450e5eafb9c2841049
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506996"
---
# <a name="access-httpcontext-in-aspnet-core"></a>ASP.NET Core에서 HttpContext에 액세스

ASP.NET Core 앱은 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 인터페이스 및 기본 구현 <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>을(를) 통해 `HttpContext`에 액세스합니다. 서비스 내에서 `HttpContext`에 액세스가 필요한 경우에만 `IHttpContextAccessor`를 사용할 필요가 있습니다.

## <a name="use-httpcontext-from-no-locrazor-pages"></a>Razor Pages에서 HttpContext 사용

Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>은 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 속성을 공개합니다.

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a>Razor 뷰에서 HttpContext 사용

Razor 뷰는 뷰에서 [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) 속성을 통해 `HttpContext`를 직접 공개합니다. 다음 예제에서는 Windows 인증을 사용하여 인트라넷 앱에서 현재 사용자 이름을 검색합니다.

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>컨트롤러에서 HttpContext 사용

컨트롤러는 다음과 같이 [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) 속성을 공개합니다.

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>미들웨어에서 HttpContext 사용

사용자 지정 미들웨어 구성 요소를 사용할 경우 `HttpContext`는 `Invoke` 또는 `InvokeAsync` 메서드로 전달되며 미들웨어가 구성될 때 액세스할 수 있습니다.

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>사용자 지정 구성 요소에서 HttpContext 사용

`HttpContext`에 액세스해야 하는 기타 프레임워크 및 사용자 지정 구성 요소의 경우 기본 제공 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너를 사용하여 종속성을 등록하는 것이 좋습니다. 종속성 주입 컨테이너는 `IHttpContextAccessor`를 해당 생성자에서 종속성으로 선언하는 모든 클래스에 이를 제공합니다.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

다음 예제에서는

* `UserRepository`는 `IHttpContextAccessor`에 대한 종속성을 선언합니다.
* 종속성 주입이 종속성 체인을 확인하고 `UserRepository` 인스턴스를 만들 경우 종속성이 제공됩니다.

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>백그라운드 스레드에서 HttpContext 액세스

`HttpContext`은(는) 스레드로부터 안전하지 않습니다. 요청을 처리하지 않고 `HttpContext`의 속성을 읽거나 쓰면 <xref:System.NullReferenceException>이 나타날 수 있습니다.

> [!NOTE]
> 앱에서 드물게 발생하는 `NullReferenceException` 오류를 생성하는 경우 백그라운드 처리를 시작하거나 요청이 완료된 후 처리를 계속하는 코드의 일부를 검토합니다. 컨트롤러 메서드를 `async void`로 정의하는 것과 같은 오류를 찾습니다.

`HttpContext` 데이터로 백그라운드 작업을 안전하게 수행하려면:

* 요청 처리 중에 필요한 데이터를 복사합니다.
* 복사된 데이터를 백그라운드 작업에 전달합니다.

안전하지 않은 코드를 방지하려면 백그라운드 작업을 수행하는 메서드에 `HttpContext`을(를) 전달하지 마세요. 필요한 데이터를 대신 전달하세요. 다음 예제에서는 전자 메일 보내기를 시작하기 위해 `SendEmailCore`을(를) 호출합니다. `correlationId`은(는) `HttpContext`이(가) 아닌, `SendEmailCore`에 전달됩니다. `SendEmailCore`이(가) 완료될 때까지 코드 실행이 대기하지 않습니다.

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a>Blazor 및 공유 상태

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
