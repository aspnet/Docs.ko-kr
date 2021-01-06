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
ms.openlocfilehash: 1678f1b4af2c65e3b10c66f7ccdbecf19156a834
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97865566"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="13a6c-103">ASP.NET Core의 단순 권한 부여</span><span class="sxs-lookup"><span data-stu-id="13a6c-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="13a6c-104">ASP.NET Core의 권한 부여는 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 및 해당 하는 다양 한 매개 변수를 사용 하 여 제어 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="13a6c-105">가장 간단한 형태의 `[Authorize]` 특성을 컨트롤러, 작업 또는 페이지에 적용 하면 Razor 해당 구성 요소에 대 한 액세스를 인증 된 사용자로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="13a6c-106">예를 들어 다음 코드는에 대 한 액세스를 `AccountController` 인증 된 사용자로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="13a6c-107">컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 `AuthorizeAttribute` 작업 자체에 특성을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="13a6c-108">이제 인증 된 사용자만 함수에 액세스할 수 있습니다 `Logout` .</span><span class="sxs-lookup"><span data-stu-id="13a6c-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="13a6c-109">또한 특성을 사용 `AllowAnonymous` 하 여 인증 되지 않은 사용자의 개별 작업에 대 한 액세스를 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="13a6c-110">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-110">For example:</span></span>

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

<span data-ttu-id="13a6c-111">이렇게 하면 인증 된 사용자 `AccountController` `Login` 또는 인증 되지 않은/익명 상태에 관계 없이 모든 사용자가 액세스할 수 있는 동작을 제외 하 고는 인증 된 사용자만을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="13a6c-112">`[AllowAnonymous]` 모든 권한 부여 문을 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="13a6c-113">`[AllowAnonymous]`및 특성을 함께 사용할 경우 `[Authorize]` `[Authorize]` 특성이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="13a6c-114">예를 들어 `[AllowAnonymous]` 컨트롤러 수준에서를 적용 하는 경우 `[Authorize]` 동일한 컨트롤러의 모든 특성 (또는 그 안에 있는 모든 작업)은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a><span data-ttu-id="13a6c-115">특성 및 Razor Pages 권한 부여</span><span class="sxs-lookup"><span data-stu-id="13a6c-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="13a6c-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>페이지 처리기에는 \*를 적용할 수 **없습니다** Razor .</span><span class="sxs-lookup"><span data-stu-id="13a6c-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can \***not** _ be applied to Razor Page handlers.</span></span> <span data-ttu-id="13a6c-117">예를 들어,, `[Authorize]` `OnGet` `OnPost` 또는 다른 페이지 처리기에는를 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="13a6c-118">다른 처리기에 대 한 권한 부여 요구 사항이 서로 다른 페이지에는 ASP.NET Core MVC 컨트롤러를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="13a6c-119">다음 두 가지 방법을 사용 하 여 페이지 처리기 메서드에 권한 부여를 적용할 수 있습니다 Razor .</span><span class="sxs-lookup"><span data-stu-id="13a6c-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

<span data-ttu-id="13a6c-120">_ 다른 권한 부여를 필요로 하는 페이지 처리기의 경우 별도의 페이지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-120">_ Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="13a6c-121">공유 콘텐츠를 하나 이상의 [부분 뷰로](xref:mvc/views/partial)이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-121">Move shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="13a6c-122">가능 하면 권장 되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="13a6c-123">공용 페이지를 공유 해야 하는 콘텐츠의 경우 권한 부여를 Iasyncpagefilter의 일부로 수행 하는 필터를 작성 합니다 [. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="13a6c-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="13a6c-124">[Pagehandlerauth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub 프로젝트는이 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="13a6c-125">[AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) 은 권한 부여 필터를 구현 합니다.[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="13a6c-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="13a6c-126">[[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) 특성은 페이지 처리기에 적용 됩니다 `OnGet` .[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="13a6c-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="13a6c-127">[Pagehandlerauth](https://github.com/pranavkm/PageHandlerAuth) 샘플 접근 방식은 페이지,페이지 모델 또는 전역에 적용 되는 권한 부여 특성을 사용 하 여 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does \***not** _: _ Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="13a6c-128">권한 부여 특성을 작성 하면 하나 이상의 `AuthorizeAttribute` 또는 `AuthorizeFilter` 인스턴스가 페이지에도 적용 될 때 인증 및 권한 부여가 여러 번 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-128">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="13a6c-129">ASP.NET Core 인증 및 권한 부여 시스템의 나머지 부분과 함께 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-129">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="13a6c-130">응용 프로그램에서이 접근 방식을 사용 하는 것이 제대로 작동 하는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13a6c-130">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="13a6c-131">페이지 처리기에서를 지원할 계획이 없습니다 `AuthorizeAttribute` Razor .</span><span class="sxs-lookup"><span data-stu-id="13a6c-131">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
