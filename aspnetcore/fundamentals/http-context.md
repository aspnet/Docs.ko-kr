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
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506996"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="e8b31-103">ASP.NET Core에서 HttpContext에 액세스</span><span class="sxs-lookup"><span data-stu-id="e8b31-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="e8b31-104">ASP.NET Core 앱은 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 인터페이스 및 기본 구현 <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>을(를) 통해 `HttpContext`에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="e8b31-105">서비스 내에서 `HttpContext`에 액세스가 필요한 경우에만 `IHttpContextAccessor`를 사용할 필요가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-no-locrazor-pages"></a><span data-ttu-id="e8b31-106">Razor Pages에서 HttpContext 사용</span><span class="sxs-lookup"><span data-stu-id="e8b31-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="e8b31-107">Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>은 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 속성을 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-no-locrazor-view"></a><span data-ttu-id="e8b31-108">Razor 뷰에서 HttpContext 사용</span><span class="sxs-lookup"><span data-stu-id="e8b31-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="e8b31-109">Razor 뷰는 뷰에서 [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) 속성을 통해 `HttpContext`를 직접 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="e8b31-110">다음 예제에서는 Windows 인증을 사용하여 인트라넷 앱에서 현재 사용자 이름을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="e8b31-111">컨트롤러에서 HttpContext 사용</span><span class="sxs-lookup"><span data-stu-id="e8b31-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="e8b31-112">컨트롤러는 다음과 같이 [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) 속성을 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="e8b31-113">미들웨어에서 HttpContext 사용</span><span class="sxs-lookup"><span data-stu-id="e8b31-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="e8b31-114">사용자 지정 미들웨어 구성 요소를 사용할 경우 `HttpContext`는 `Invoke` 또는 `InvokeAsync` 메서드로 전달되며 미들웨어가 구성될 때 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="e8b31-115">사용자 지정 구성 요소에서 HttpContext 사용</span><span class="sxs-lookup"><span data-stu-id="e8b31-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="e8b31-116">`HttpContext`에 액세스해야 하는 기타 프레임워크 및 사용자 지정 구성 요소의 경우 기본 제공 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너를 사용하여 종속성을 등록하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e8b31-117">종속성 주입 컨테이너는 `IHttpContextAccessor`를 해당 생성자에서 종속성으로 선언하는 모든 클래스에 이를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="e8b31-118">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="e8b31-118">In the following example:</span></span>

* <span data-ttu-id="e8b31-119">`UserRepository`는 `IHttpContextAccessor`에 대한 종속성을 선언합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="e8b31-120">종속성 주입이 종속성 체인을 확인하고 `UserRepository` 인스턴스를 만들 경우 종속성이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="e8b31-121">백그라운드 스레드에서 HttpContext 액세스</span><span class="sxs-lookup"><span data-stu-id="e8b31-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="e8b31-122">`HttpContext`은(는) 스레드로부터 안전하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="e8b31-123">요청을 처리하지 않고 `HttpContext`의 속성을 읽거나 쓰면 <xref:System.NullReferenceException>이 나타날 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="e8b31-124">앱에서 드물게 발생하는 `NullReferenceException` 오류를 생성하는 경우 백그라운드 처리를 시작하거나 요청이 완료된 후 처리를 계속하는 코드의 일부를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="e8b31-125">컨트롤러 메서드를 `async void`로 정의하는 것과 같은 오류를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="e8b31-126">`HttpContext` 데이터로 백그라운드 작업을 안전하게 수행하려면:</span><span class="sxs-lookup"><span data-stu-id="e8b31-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="e8b31-127">요청 처리 중에 필요한 데이터를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="e8b31-128">복사된 데이터를 백그라운드 작업에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="e8b31-129">안전하지 않은 코드를 방지하려면 백그라운드 작업을 수행하는 메서드에 `HttpContext`을(를) 전달하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="e8b31-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="e8b31-130">필요한 데이터를 대신 전달하세요.</span><span class="sxs-lookup"><span data-stu-id="e8b31-130">Pass the required data instead.</span></span> <span data-ttu-id="e8b31-131">다음 예제에서는 전자 메일 보내기를 시작하기 위해 `SendEmailCore`을(를) 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="e8b31-132">`correlationId`은(는) `HttpContext`이(가) 아닌, `SendEmailCore`에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="e8b31-133">`SendEmailCore`이(가) 완료될 때까지 코드 실행이 대기하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e8b31-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="e8b31-134">Blazor 및 공유 상태</span><span class="sxs-lookup"><span data-stu-id="e8b31-134">Blazor and shared state</span></span>

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
