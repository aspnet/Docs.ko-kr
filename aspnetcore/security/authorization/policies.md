---
title: ASP.NET Core에서 정책 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 앱에서 권한 부여 요구 사항을 적용 하기 위한 권한 부여 정책 처리기를 만들고 사용 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: 286dc3bcc66b86a2a6b7d3cb7b6052bf7b474aff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060211"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="4a075-103">ASP.NET Core에서 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="4a075-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4a075-104">내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="4a075-105">이러한 구성 요소는 코드에서 권한 부여 평가 식을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="4a075-106">그 결과는 더 풍부 하 고 재사용 가능한 테스트 가능한 권한 부여 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="4a075-107">권한 부여 정책은 하나 이상의 요구 사항으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="4a075-108">메서드는 권한 부여 서비스 구성의 일부로 등록 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4a075-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="4a075-109">위의 예에서는 "AtLeast21" 정책이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="4a075-110">&mdash;요구 사항에 대 한 매개 변수로 제공 되는 최소 연령의 단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="4a075-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="4a075-111">IAuthorizationService</span></span>

<span data-ttu-id="4a075-112">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="4a075-113">위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="4a075-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 는 메서드가 없는 마커 서비스 이며, 권한 부여의 성공 여부를 추적 하는 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="4a075-115">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 는 요구 사항이 충족 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="4a075-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="4a075-117">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="4a075-118">다음 코드에서는 일반적인을 보여 줍니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4a075-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();
}
```

<span data-ttu-id="4a075-119"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 권한 부여에 또는를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="4a075-120">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="4a075-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="4a075-121">페이지를 사용 하 :::no-loc(Razor)::: 는 경우이 문서의 [ :::no-loc(Razor)::: 페이지에 정책 적용](#apply-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4a075-121">If you're using :::no-loc(Razor)::: Pages, see [Apply policies to :::no-loc(Razor)::: Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="4a075-122">정책은 정책 이름으로 특성을 사용 하 여 컨트롤러에 적용 됩니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="4a075-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="4a075-123">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="4a075-124">페이지에 정책 적용 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="4a075-124">Apply policies to :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="4a075-125">정책은 :::no-loc(Razor)::: `[Authorize]` 정책 이름이 있는 특성을 사용 하 여 페이지에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-125">Policies are applied to :::no-loc(Razor)::: Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="4a075-126">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="4a075-127">정책은 페이지 처리기 **not** 수준에서 적용 될 수 없습니다 :::no-loc(Razor)::: . 페이지에 적용 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-127">Policies can \* **not** _ be applied at the :::no-loc(Razor)::: Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="4a075-128">:::no-loc(Razor)::: [권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 페이지에 정책을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-128">Policies can be applied to :::no-loc(Razor)::: Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="4a075-129">요구 사항</span><span class="sxs-lookup"><span data-stu-id="4a075-129">Requirements</span></span>

<span data-ttu-id="4a075-130">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="4a075-131">"AtLeast21" 정책에서 요구 사항은 최소 age의 단일 매개 변수입니다 &mdash; .</span><span class="sxs-lookup"><span data-stu-id="4a075-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="4a075-132">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="4a075-133">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="4a075-134">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="4a075-135">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 _ *및* \* 단위로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an _ *AND* \* basis.</span></span>

> [!NOTE]
> <span data-ttu-id="4a075-136">요구 사항에는 데이터 또는 속성이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="4a075-137">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="4a075-137">Authorization handlers</span></span>

<span data-ttu-id="4a075-138">권한 부여 처리기는 요구 사항의 속성을 평가 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="4a075-139">권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="4a075-140">요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="4a075-141">처리기는 [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)를 상속할 수 있습니다 `TRequirement` . 여기서은 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="4a075-142">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="4a075-143">한 가지 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="4a075-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="4a075-144">다음은 최소 사용 기간 처리기에서 단일 요구 사항을 활용 하는 일 대 일 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="4a075-145">위의 코드는 현재 사용자 보안 주체에 알려진 발급자와 신뢰할 수 있는 발급자가 발급 한 날짜가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="4a075-146">클레임이 없는 경우 권한 부여를 수행할 수 없습니다 .이 경우 완료 된 작업이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="4a075-147">클레임이 있는 경우 사용자의 나이가 계산 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="4a075-148">사용자가 요구 사항에 정의 된 최소 보존 기간을 충족 하면 권한 부여가 성공으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="4a075-149">권한 부여가 성공 하면 `context.Succeed` 은 충족 되는 요구 사항을 유일한 매개 변수로 사용 하 여 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="4a075-150">여러 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="4a075-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="4a075-151">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="4a075-152">이전 코드는 [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 성공으로 표시 되지 않은 요구 사항이 포함 된 속성을 pendingrequirements 사항으로 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="4a075-153">`ReadPermission`요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="4a075-154">`EditPermission`또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="4a075-155">처리기 등록</span><span class="sxs-lookup"><span data-stu-id="4a075-155">Handler registration</span></span>

<span data-ttu-id="4a075-156">처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="4a075-157">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="4a075-158">위의 코드는를 `MinimumAgeHandler` 호출 하 여 singleton으로 등록 합니다 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="4a075-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="4a075-159">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="4a075-160">처리기에서 반환 해야 하는 작업</span><span class="sxs-lookup"><span data-stu-id="4a075-160">What should a handler return?</span></span>

<span data-ttu-id="4a075-161">`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="4a075-162">성공 또는 실패의 상태는 어떻게 표시 되나요?</span><span class="sxs-lookup"><span data-stu-id="4a075-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="4a075-163">처리기는를 호출 하 여 `context.Succeed(IAuthorizationRequirement requirement)` 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="4a075-164">같은 요구 사항에 대 한 다른 처리기가 성공할 수 있으므로 처리기는 일반적으로 오류를 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="4a075-165">오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하더라도를 호출 `context.Fail` 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="4a075-166">처리기에서 또는를 호출 하는 경우 `context.Succeed` `context.Fail` 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="4a075-167">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="4a075-168">로 설정 된 경우 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은가 호출 될 때 처리기의 실행을 short 회로 합니다 `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="4a075-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="4a075-169">`InvokeHandlersAfterFailure` 는 기본적으로로 설정 `true` 되며,이 경우 모든 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="4a075-170">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="4a075-171">요구 사항에 대 한 여러 처리기가 필요한 이유는 무엇 인가요?</span><span class="sxs-lookup"><span data-stu-id="4a075-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="4a075-172">평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="4a075-173">예를 들어 Microsoft에는 키 카드만 여는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="4a075-174">키 카드를 집에서 벗어나면 접수원는 임시 스티커를 인쇄 하 고 도어를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="4a075-175">이 시나리오에서는 단일 요구 사항이 *BuildingEntry* 하나는 단일 요구 사항을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-175">In this scenario, you'd have a single requirement, *BuildingEntry* , but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="4a075-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="4a075-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="4a075-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="4a075-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="4a075-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="4a075-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="4a075-179">두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="4a075-180">정책에서을 평가할 때 두 핸들러가 모두 성공 하면 `BuildingEntryRequirement` 정책 평가가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="4a075-181">Func를 사용 하 여 정책 수행</span><span class="sxs-lookup"><span data-stu-id="4a075-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="4a075-182">정책을 처리 하는 것이 간단 하 게 코드를 표현할 수 있는 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="4a075-183">`Func<AuthorizationHandlerContext, bool>`정책 작성기를 사용 하 여 정책을 구성할 때를 제공할 수 있습니다 `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="4a075-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="4a075-184">예를 들어 이전은 다음과 `BadgeEntryHandler` 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="4a075-185">처리기의 MVC 요청 컨텍스트 액세스</span><span class="sxs-lookup"><span data-stu-id="4a075-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="4a075-186">`HandleRequirementAsync`권한 부여 처리기에서 구현 하는 메서드에는 두 개의 매개 변수인 `AuthorizationHandlerContext` 및를 `TRequirement` 처리 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="4a075-187">MVC 또는와 같은 프레임 워크 :::no-loc(SignalR)::: `Resource` 는 `AuthorizationHandlerContext` 추가 정보를 전달 하기 위해 개체를의 속성에 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-187">Frameworks such as MVC or :::no-loc(SignalR)::: are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="4a075-188">끝점 라우팅을 사용 하는 경우 권한 부여는 일반적으로 권한 부여 미들웨어에 의해 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="4a075-189">이 경우 `Resource` 속성은의 인스턴스입니다 <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="4a075-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="4a075-190">끝점을 사용 하 여 라우팅하는 기본 리소스를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="4a075-191">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="4a075-192">끝점이 현재에 대 한 액세스를 제공 하지 않습니다 `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="4a075-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="4a075-193">끝점 라우팅을 사용 하는 경우를 사용 `IHttpContextAcessor` 하 여 `HttpContext` 권한 부여 처리기 내에서 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="4a075-194">자세한 내용은 [사용자 지정 구성 요소에서 HttpContext 사용](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4a075-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="4a075-195">기존 라우팅을 사용 하거나, MVC의 권한 부여 필터의 일부로 권한 부여가 수행 되는 경우의 값은 `Resource` <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 인스턴스입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="4a075-196">이 속성은 `HttpContext` `RouteData` MVC 및 페이지에서 제공 되는, 및 모든에 대 한 액세스를 제공 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="4a075-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="4a075-197">속성을 사용 하는 `Resource` 것은 프레임 워크와 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="4a075-198">속성의 정보를 사용 하면 `Resource` 권한 부여 정책이 특정 프레임 워크로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="4a075-199">`Resource`키워드를 사용 하 여 속성을 캐스팅 한 `is` 다음, `InvalidCastException` 다른 프레임 워크에서 실행 될 때 코드가와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="4a075-200">모든 사용자를 전역적으로 인증 해야 함</span><span class="sxs-lookup"><span data-stu-id="4a075-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4a075-201">내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="4a075-202">이러한 구성 요소는 코드에서 권한 부여 평가 식을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="4a075-203">그 결과는 더 풍부 하 고 재사용 가능한 테스트 가능한 권한 부여 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="4a075-204">권한 부여 정책은 하나 이상의 요구 사항으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="4a075-205">메서드는 권한 부여 서비스 구성의 일부로 등록 됩니다 `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4a075-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="4a075-206">위의 예에서는 "AtLeast21" 정책이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="4a075-207">&mdash;요구 사항에 대 한 매개 변수로 제공 되는 최소 연령의 단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="4a075-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="4a075-208">IAuthorizationService</span></span> 

<span data-ttu-id="4a075-209">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="4a075-210">위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="4a075-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 는 메서드가 없는 마커 서비스 이며, 권한 부여의 성공 여부를 추적 하는 메커니즘입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="4a075-212">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 는 요구 사항이 충족 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="4a075-213"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="4a075-214">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="4a075-215">다음 코드에서는 일반적인을 보여 줍니다 `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4a075-215">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="4a075-216"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 권한 부여에 또는를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="4a075-217">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="4a075-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="4a075-218">페이지를 사용 하 :::no-loc(Razor)::: 는 경우이 문서의 [ :::no-loc(Razor)::: 페이지에 정책 적용](#apply-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4a075-218">If you're using :::no-loc(Razor)::: Pages, see [Apply policies to :::no-loc(Razor)::: Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="4a075-219">정책은 정책 이름으로 특성을 사용 하 여 컨트롤러에 적용 됩니다 `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="4a075-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="4a075-220">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="4a075-221">페이지에 정책 적용 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="4a075-221">Apply policies to :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="4a075-222">정책은 :::no-loc(Razor)::: `[Authorize]` 정책 이름이 있는 특성을 사용 하 여 페이지에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-222">Policies are applied to :::no-loc(Razor)::: Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="4a075-223">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="4a075-224">:::no-loc(Razor)::: [권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 페이지에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-224">Policies can also be applied to :::no-loc(Razor)::: Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="4a075-225">요구 사항</span><span class="sxs-lookup"><span data-stu-id="4a075-225">Requirements</span></span>

<span data-ttu-id="4a075-226">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="4a075-227">"AtLeast21" 정책에서 요구 사항은 최소 age의 단일 매개 변수입니다 &mdash; .</span><span class="sxs-lookup"><span data-stu-id="4a075-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="4a075-228">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="4a075-229">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="4a075-230">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="4a075-231">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="4a075-232">요구 사항에는 데이터 또는 속성이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="4a075-233">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="4a075-233">Authorization handlers</span></span>

<span data-ttu-id="4a075-234">권한 부여 처리기는 요구 사항의 속성을 평가 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="4a075-235">권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="4a075-236">요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="4a075-237">처리기는 [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)를 상속할 수 있습니다 `TRequirement` . 여기서은 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="4a075-238">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="4a075-239">한 가지 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="4a075-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="4a075-240">다음은 최소 사용 기간 처리기에서 단일 요구 사항을 활용 하는 일 대 일 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="4a075-241">위의 코드는 현재 사용자 보안 주체에 알려진 발급자와 신뢰할 수 있는 발급자가 발급 한 날짜가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="4a075-242">클레임이 없는 경우 권한 부여를 수행할 수 없습니다 .이 경우 완료 된 작업이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="4a075-243">클레임이 있는 경우 사용자의 나이가 계산 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="4a075-244">사용자가 요구 사항에 정의 된 최소 보존 기간을 충족 하면 권한 부여가 성공으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="4a075-245">권한 부여가 성공 하면 `context.Succeed` 은 충족 되는 요구 사항을 유일한 매개 변수로 사용 하 여 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="4a075-246">여러 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="4a075-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="4a075-247">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="4a075-248">이전 코드는 [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 성공으로 표시 되지 않은 요구 사항이 포함 된 속성을 pendingrequirements 사항으로 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="4a075-249">`ReadPermission`요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="4a075-250">`EditPermission`또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="4a075-251">처리기 등록</span><span class="sxs-lookup"><span data-stu-id="4a075-251">Handler registration</span></span>

<span data-ttu-id="4a075-252">처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="4a075-253">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="4a075-254">위의 코드는를 `MinimumAgeHandler` 호출 하 여 singleton으로 등록 합니다 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="4a075-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="4a075-255">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="4a075-256">처리기에서 반환 해야 하는 작업</span><span class="sxs-lookup"><span data-stu-id="4a075-256">What should a handler return?</span></span>

<span data-ttu-id="4a075-257">`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="4a075-258">성공 또는 실패의 상태는 어떻게 표시 되나요?</span><span class="sxs-lookup"><span data-stu-id="4a075-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="4a075-259">처리기는를 호출 하 여 `context.Succeed(IAuthorizationRequirement requirement)` 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="4a075-260">같은 요구 사항에 대 한 다른 처리기가 성공할 수 있으므로 처리기는 일반적으로 오류를 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="4a075-261">오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하더라도를 호출 `context.Fail` 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="4a075-262">처리기에서 또는를 호출 하는 경우 `context.Succeed` `context.Fail` 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="4a075-263">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="4a075-264">로 설정 된 경우 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은가 호출 될 때 처리기의 실행을 short 회로 합니다 `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="4a075-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="4a075-265">`InvokeHandlersAfterFailure` 는 기본적으로로 설정 `true` 되며,이 경우 모든 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="4a075-266">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="4a075-267">요구 사항에 대 한 여러 처리기가 필요한 이유는 무엇 인가요?</span><span class="sxs-lookup"><span data-stu-id="4a075-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="4a075-268">평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="4a075-269">예를 들어 Microsoft에는 키 카드만 여는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="4a075-270">키 카드를 집에서 벗어나면 접수원는 임시 스티커를 인쇄 하 고 도어를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="4a075-271">이 시나리오에서는 단일 요구 사항이 *BuildingEntry* 하나는 단일 요구 사항을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-271">In this scenario, you'd have a single requirement, *BuildingEntry* , but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="4a075-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="4a075-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="4a075-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="4a075-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="4a075-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="4a075-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="4a075-275">두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="4a075-276">정책에서을 평가할 때 두 핸들러가 모두 성공 하면 `BuildingEntryRequirement` 정책 평가가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="4a075-277">Func를 사용 하 여 정책 수행</span><span class="sxs-lookup"><span data-stu-id="4a075-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="4a075-278">정책을 처리 하는 것이 간단 하 게 코드를 표현할 수 있는 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="4a075-279">`Func<AuthorizationHandlerContext, bool>`정책 작성기를 사용 하 여 정책을 구성할 때를 제공할 수 있습니다 `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="4a075-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="4a075-280">예를 들어 이전은 다음과 `BadgeEntryHandler` 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="4a075-281">처리기의 MVC 요청 컨텍스트 액세스</span><span class="sxs-lookup"><span data-stu-id="4a075-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="4a075-282">`HandleRequirementAsync`권한 부여 처리기에서 구현 하는 메서드에는 두 개의 매개 변수인 `AuthorizationHandlerContext` 및를 `TRequirement` 처리 하 고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="4a075-283">MVC 또는와 같은 프레임 워크 :::no-loc(SignalR)::: `Resource` 는 `AuthorizationHandlerContext` 추가 정보를 전달 하기 위해 개체를의 속성에 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-283">Frameworks such as MVC or :::no-loc(SignalR)::: are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="4a075-284">예를 들어, MVC는 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 `Resource` 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="4a075-285">이 속성은 `HttpContext` `RouteData` MVC 및 페이지에서 제공 되는, 및 모든에 대 한 액세스를 제공 합니다 :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="4a075-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="4a075-286">속성을 사용 하는 `Resource` 것은 프레임 워크와 관련이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="4a075-287">속성의 정보를 사용 하면 `Resource` 권한 부여 정책이 특정 프레임 워크로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="4a075-288">`Resource`키워드를 사용 하 여 속성을 캐스팅 한 `is` 다음, `InvalidCastException` 다른 프레임 워크에서 실행 될 때 코드가와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4a075-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
