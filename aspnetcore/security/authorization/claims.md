---
title: ASP.NET Core에서 클레임 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 앱에서 권한 부여에 대 한 클레임 확인을 추가 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/claims
ms.openlocfilehash: d6317da6bca69b4c46d74a2f76d81af4059d1cd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060276"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="f0003-103">ASP.NET Core에서 클레임 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="f0003-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="f0003-104">Id가 생성 되 면 신뢰할 수 있는 당사자가 발급 한 하나 이상의 클레임이 할당 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="f0003-105">클레임은 주체가 어떤 작업을 수행할 수 있는지를 나타내는 이름 값 쌍입니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="f0003-106">예를 들어 로컬 구동 라이선스 기관에서 발급 한 드라이버 라이선스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="f0003-107">드라이버의 라이선스에 대 한 생일이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="f0003-108">이 경우 클레임 이름은이 `DateOfBirth` 고 클레임 값은 생년월일입니다. 예를 들어 `8th June 1970` 발급자가 운전 라이선스 기관이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="f0003-109">가장 간단한 클레임 기반 권한 부여는 클레임 값을 확인 하 고 해당 값에 따라 리소스에 대 한 액세스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="f0003-110">예를 들어 야간 클럽에 액세스 하려는 경우 권한 부여 프로세스는 다음과 같을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="f0003-111">도어 보안 책임자는 액세스 권한을 부여 하기 전에 생년월일 클레임의 값과 발급자 (운전 라이선스 기관)를 신뢰 하는지 여부를 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="f0003-112">Id는 여러 개의 값이 있는 여러 클레임을 포함할 수 있으며, 동일한 유형의 클레임을 여러 개 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="f0003-113">클레임 확인 추가</span><span class="sxs-lookup"><span data-stu-id="f0003-113">Adding claims checks</span></span>

<span data-ttu-id="f0003-114">클레임 기반 권한 부여 확인은 선언적 이며, 개발자는 코드 내에이를 포함 하 여 컨트롤러 또는 컨트롤러 내의 작업에 대해 현재 사용자가 소유 해야 하는 클레임을 지정 하 고 필요에 따라 클레임에서 요청 된 리소스에 액세스 하기 위해 보유 해야 하는 값을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="f0003-115">클레임 요구 사항은 정책 기반 이므로 개발자는 클레임 요구 사항을 나타내는 정책을 작성 하 고 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="f0003-116">가장 간단한 유형의 클레임 정책은 클레임의 존재를 찾고 값을 확인 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="f0003-117">먼저 정책을 빌드하고 등록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-117">First you need to build and register the policy.</span></span> <span data-ttu-id="f0003-118">이는 일반적으로 Startup.cs 파일의 일부를 사용 하는 권한 부여 서비스 구성의 일부로 수행 `ConfigureServices()` 됩니다. *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="f0003-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="f0003-119">이 경우 `EmployeeOnly` 정책은 `EmployeeNumber` 현재 id에 클레임이 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="f0003-120">그런 다음 특성의 속성을 사용 하 여 정책을 적용 하 여 `Policy` `AuthorizeAttribute` 정책 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="f0003-121">`AuthorizeAttribute`특성은 전체 컨트롤러에 적용할 수 있습니다 .이 경우 정책과 일치 하는 id만 컨트롤러의 모든 작업에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="f0003-122">특성으로 보호 되는 컨트롤러가 `AuthorizeAttribute` 있지만 특정 작업에 대 한 익명 액세스를 허용 하려는 경우에는 특성을 적용 `AllowAnonymousAttribute` 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="f0003-123">대부분의 클레임에는 값이 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-123">Most claims come with a value.</span></span> <span data-ttu-id="f0003-124">정책을 만들 때 허용 되는 값 목록을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="f0003-125">다음 예는 직원 번호가 1, 2, 3, 4 또는 5 인 직원 에게만 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="f0003-126">일반 클레임 확인 추가</span><span class="sxs-lookup"><span data-stu-id="f0003-126">Add a generic claim check</span></span>

<span data-ttu-id="f0003-127">클레임 값이 단일 값이 아니거나 변환이 필요한 경우 [Requireassertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="f0003-128">자세한 내용은 [함수를 사용 하 여 정책 수행](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="f0003-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="f0003-129">여러 정책 평가</span><span class="sxs-lookup"><span data-stu-id="f0003-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="f0003-130">컨트롤러 또는 작업에 정책을 여러 개 적용 하는 경우 액세스가 부여 되기 전에 모든 정책이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="f0003-131">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="f0003-132">위의 예제에서 정책을 충족 하는 모든 id `EmployeeOnly` `Payslip` 는 컨트롤러에서 정책이 적용 될 때 작업에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="f0003-133">그러나 동작을 호출 하기 위해 `UpdateSalary` id는 정책과 정책을 *모두* 충족 해야 합니다 `EmployeeOnly` `HumanResources` .</span><span class="sxs-lookup"><span data-stu-id="f0003-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="f0003-134">생년월일을 계산 하는 것과 같이 더 복잡 한 정책을 원하는 경우에는 해당 기간을 계산 하 고 나이를 계산 하 고 나 서이를 확인 한 후에 [사용자 지정 정책 처리기](xref:security/authorization/policies)를 작성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f0003-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
