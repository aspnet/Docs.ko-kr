---
title: ASP.NET Core에서 정책 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 앱에서 권한 부여 요구 사항을 적용 하기 위한 권한 부여 정책 처리기를 만들고 사용 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: 286dc3bcc66b86a2a6b7d3cb7b6052bf7b474aff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060211"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core에서 정책 기반 권한 부여

::: moniker range=">= aspnetcore-3.0"

내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다. 이러한 구성 요소는 코드에서 권한 부여 평가 식을 지원 합니다. 그 결과는 더 풍부 하 고 재사용 가능한 테스트 가능한 권한 부여 구조입니다.

권한 부여 정책은 하나 이상의 요구 사항으로 구성 됩니다. 메서드는 권한 부여 서비스 구성의 일부로 등록 됩니다 `Startup.ConfigureServices` .

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

위의 예에서는 "AtLeast21" 정책이 만들어집니다. &mdash;요구 사항에 대 한 매개 변수로 제공 되는 최소 연령의 단일 요구 사항이 있습니다.

## <a name="iauthorizationservice"></a>IAuthorizationService

권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 다음과 같습니다.

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 는 메서드가 없는 마커 서비스 이며, 권한 부여의 성공 여부를 추적 하는 메커니즘입니다.

각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 는 요구 사항이 충족 되는지 확인 합니다.
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

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.

```csharp
 context.Succeed(requirement)
```

다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.

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

다음 코드에서는 일반적인을 보여 줍니다 `ConfigureServices` .

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
    services.AddRazorPages();
}
```

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 권한 부여에 또는를 사용 합니다.

## <a name="apply-policies-to-mvc-controllers"></a>MVC 컨트롤러에 정책 적용

페이지를 사용 하 Razor 는 경우이 문서의 [ Razor 페이지에 정책 적용](#apply-policies-to-razor-pages) 을 참조 하세요.

정책은 정책 이름으로 특성을 사용 하 여 컨트롤러에 적용 됩니다 `[Authorize]` . 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>페이지에 정책 적용 Razor

정책은 Razor `[Authorize]` 정책 이름이 있는 특성을 사용 하 여 페이지에 적용 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

정책은 페이지 처리기 **not** 수준에서 적용 될 수 없습니다 Razor . 페이지에 적용 되어야 합니다.

Razor [권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 페이지에 정책을 적용할 수 있습니다.

## <a name="requirements"></a>요구 사항

권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다. "AtLeast21" 정책에서 요구 사항은 최소 age의 단일 매개 변수입니다 &mdash; . 요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다. 매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다. 즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 _ *및* * 단위로 처리 됩니다.

> [!NOTE]
> 요구 사항에는 데이터 또는 속성이 필요 하지 않습니다.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>권한 부여 처리기

권한 부여 처리기는 요구 사항의 속성을 평가 하는 일을 담당 합니다. 권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.

요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다. 처리기는 [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)를 상속할 수 있습니다 `TRequirement` . 여기서은 처리 해야 하는 요구 사항입니다. 또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.

### <a name="use-a-handler-for-one-requirement"></a>한 가지 요구 사항에 대 한 처리기 사용

<a name="security-authorization-handler-example"></a>

다음은 최소 사용 기간 처리기에서 단일 요구 사항을 활용 하는 일 대 일 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

위의 코드는 현재 사용자 보안 주체에 알려진 발급자와 신뢰할 수 있는 발급자가 발급 한 날짜가 있는지 확인 합니다. 클레임이 없는 경우 권한 부여를 수행할 수 없습니다 .이 경우 완료 된 작업이 반환 됩니다. 클레임이 있는 경우 사용자의 나이가 계산 됩니다. 사용자가 요구 사항에 정의 된 최소 보존 기간을 충족 하면 권한 부여가 성공으로 간주 됩니다. 권한 부여가 성공 하면 `context.Succeed` 은 충족 되는 요구 사항을 유일한 매개 변수로 사용 하 여 호출 됩니다.

### <a name="use-a-handler-for-multiple-requirements"></a>여러 요구 사항에 대 한 처리기 사용

다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

이전 코드는 [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 성공으로 표시 되지 않은 요구 사항이 포함 된 속성을 pendingrequirements 사항으로 트래버스 합니다. `ReadPermission`요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다. `EditPermission`또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>처리기 등록

처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

위의 코드는를 `MinimumAgeHandler` 호출 하 여 singleton으로 등록 합니다 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.

## <a name="what-should-a-handler-return"></a>처리기에서 반환 해야 하는 작업

`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환 하지 않습니다. 성공 또는 실패의 상태는 어떻게 표시 되나요?

* 처리기는를 호출 하 여 `context.Succeed(IAuthorizationRequirement requirement)` 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 성공을 나타냅니다.

* 같은 요구 사항에 대 한 다른 처리기가 성공할 수 있으므로 처리기는 일반적으로 오류를 처리할 필요가 없습니다.

* 오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하더라도를 호출 `context.Fail` 합니다.

처리기에서 또는를 호출 하는 경우 `context.Succeed` `context.Fail` 다른 모든 처리기가 계속 호출 됩니다. 이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다. 로 설정 된 경우 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은가 호출 될 때 처리기의 실행을 short 회로 합니다 `context.Fail` . `InvokeHandlersAfterFailure` 는 기본적으로로 설정 `true` 되며,이 경우 모든 처리기가 호출 됩니다.

> [!NOTE]
> 인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>요구 사항에 대 한 여러 처리기가 필요한 이유는 무엇 인가요?

평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다. 예를 들어 Microsoft에는 키 카드만 여는 문이 있습니다. 키 카드를 집에서 벗어나면 접수원는 임시 스티커를 인쇄 하 고 도어를 엽니다. 이 시나리오에서는 단일 요구 사항이 *BuildingEntry* 하나는 단일 요구 사항을 검사 합니다.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다. 정책에서을 평가할 때 두 핸들러가 모두 성공 하면 `BuildingEntryRequirement` 정책 평가가 성공 합니다.

## <a name="use-a-func-to-fulfill-a-policy"></a>Func를 사용 하 여 정책 수행

정책을 처리 하는 것이 간단 하 게 코드를 표현할 수 있는 경우가 있을 수 있습니다. `Func<AuthorizationHandlerContext, bool>`정책 작성기를 사용 하 여 정책을 구성할 때를 제공할 수 있습니다 `RequireAssertion` .

예를 들어 이전은 다음과 `BadgeEntryHandler` 같이 다시 작성할 수 있습니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a>처리기의 MVC 요청 컨텍스트 액세스

`HandleRequirementAsync`권한 부여 처리기에서 구현 하는 메서드에는 두 개의 매개 변수인 `AuthorizationHandlerContext` 및를 `TRequirement` 처리 하 고 있습니다. MVC 또는와 같은 프레임 워크 SignalR `Resource` 는 `AuthorizationHandlerContext` 추가 정보를 전달 하기 위해 개체를의 속성에 자유롭게 추가할 수 있습니다.

끝점 라우팅을 사용 하는 경우 권한 부여는 일반적으로 권한 부여 미들웨어에 의해 처리 됩니다. 이 경우 `Resource` 속성은의 인스턴스입니다 <xref:Microsoft.AspNetCore.Http.Endpoint> . 끝점을 사용 하 여 라우팅하는 기본 리소스를 검색할 수 있습니다. 다음은 그 예입니다.

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

끝점이 현재에 대 한 액세스를 제공 하지 않습니다 `HttpContext` . 끝점 라우팅을 사용 하는 경우를 사용 `IHttpContextAcessor` 하 여 `HttpContext` 권한 부여 처리기 내에서 액세스 합니다. 자세한 내용은 [사용자 지정 구성 요소에서 HttpContext 사용](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components)을 참조 하세요.

기존 라우팅을 사용 하거나, MVC의 권한 부여 필터의 일부로 권한 부여가 수행 되는 경우의 값은 `Resource` <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> 인스턴스입니다. 이 속성은 `HttpContext` `RouteData` MVC 및 페이지에서 제공 되는, 및 모든에 대 한 액세스를 제공 합니다 Razor .

속성을 사용 하는 `Resource` 것은 프레임 워크와 관련이 있습니다. 속성의 정보를 사용 하면 `Resource` 권한 부여 정책이 특정 프레임 워크로 제한 됩니다. `Resource`키워드를 사용 하 여 속성을 캐스팅 한 `is` 다음, `InvalidCastException` 다른 프레임 워크에서 실행 될 때 코드가와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a>모든 사용자를 전역적으로 인증 해야 함

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다. 이러한 구성 요소는 코드에서 권한 부여 평가 식을 지원 합니다. 그 결과는 더 풍부 하 고 재사용 가능한 테스트 가능한 권한 부여 구조입니다.

권한 부여 정책은 하나 이상의 요구 사항으로 구성 됩니다. 메서드는 권한 부여 서비스 구성의 일부로 등록 됩니다 `Startup.ConfigureServices` .

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

위의 예에서는 "AtLeast21" 정책이 만들어집니다. &mdash;요구 사항에 대 한 매개 변수로 제공 되는 최소 연령의 단일 요구 사항이 있습니다.

## <a name="iauthorizationservice"></a>IAuthorizationService 

권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 다음과 같습니다.

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> 는 메서드가 없는 마커 서비스 이며, 권한 부여의 성공 여부를 추적 하는 메커니즘입니다.

각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> 는 요구 사항이 충족 되는지 확인 합니다.
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

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.

```csharp
 context.Succeed(requirement)
```

다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.

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

다음 코드에서는 일반적인을 보여 줍니다 `ConfigureServices` .

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

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` 권한 부여에 또는를 사용 합니다.

## <a name="apply-policies-to-mvc-controllers"></a>MVC 컨트롤러에 정책 적용

페이지를 사용 하 Razor 는 경우이 문서의 [ Razor 페이지에 정책 적용](#apply-policies-to-razor-pages) 을 참조 하세요.

정책은 정책 이름으로 특성을 사용 하 여 컨트롤러에 적용 됩니다 `[Authorize]` . 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>페이지에 정책 적용 Razor

정책은 Razor `[Authorize]` 정책 이름이 있는 특성을 사용 하 여 페이지에 적용 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Razor [권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 페이지에 정책을 적용할 수도 있습니다.

## <a name="requirements"></a>요구 사항

권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다. "AtLeast21" 정책에서 요구 사항은 최소 age의 단일 매개 변수입니다 &mdash; . 요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다. 매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다. 즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.

> [!NOTE]
> 요구 사항에는 데이터 또는 속성이 필요 하지 않습니다.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>권한 부여 처리기

권한 부여 처리기는 요구 사항의 속성을 평가 하는 일을 담당 합니다. 권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.

요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다. 처리기는 [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)를 상속할 수 있습니다 `TRequirement` . 여기서은 처리 해야 하는 요구 사항입니다. 또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.

### <a name="use-a-handler-for-one-requirement"></a>한 가지 요구 사항에 대 한 처리기 사용

<a name="security-authorization-handler-example"></a>

다음은 최소 사용 기간 처리기에서 단일 요구 사항을 활용 하는 일 대 일 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

위의 코드는 현재 사용자 보안 주체에 알려진 발급자와 신뢰할 수 있는 발급자가 발급 한 날짜가 있는지 확인 합니다. 클레임이 없는 경우 권한 부여를 수행할 수 없습니다 .이 경우 완료 된 작업이 반환 됩니다. 클레임이 있는 경우 사용자의 나이가 계산 됩니다. 사용자가 요구 사항에 정의 된 최소 보존 기간을 충족 하면 권한 부여가 성공으로 간주 됩니다. 권한 부여가 성공 하면 `context.Succeed` 은 충족 되는 요구 사항을 유일한 매개 변수로 사용 하 여 호출 됩니다.

### <a name="use-a-handler-for-multiple-requirements"></a>여러 요구 사항에 대 한 처리기 사용

다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

이전 코드는 [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; 성공으로 표시 되지 않은 요구 사항이 포함 된 속성을 pendingrequirements 사항으로 트래버스 합니다. `ReadPermission`요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다. `EditPermission`또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>처리기 등록

처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

위의 코드는를 `MinimumAgeHandler` 호출 하 여 singleton으로 등록 합니다 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.

## <a name="what-should-a-handler-return"></a>처리기에서 반환 해야 하는 작업

`Handle` [처리기 예제의](#security-authorization-handler-example) 메서드는 값을 반환 하지 않습니다. 성공 또는 실패의 상태는 어떻게 표시 되나요?

* 처리기는를 호출 하 여 `context.Succeed(IAuthorizationRequirement requirement)` 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 성공을 나타냅니다.

* 같은 요구 사항에 대 한 다른 처리기가 성공할 수 있으므로 처리기는 일반적으로 오류를 처리할 필요가 없습니다.

* 오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하더라도를 호출 `context.Fail` 합니다.

처리기에서 또는를 호출 하는 경우 `context.Succeed` `context.Fail` 다른 모든 처리기가 계속 호출 됩니다. 이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다. 로 설정 된 경우 `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은가 호출 될 때 처리기의 실행을 short 회로 합니다 `context.Fail` . `InvokeHandlersAfterFailure` 는 기본적으로로 설정 `true` 되며,이 경우 모든 처리기가 호출 됩니다.

> [!NOTE]
> 인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>요구 사항에 대 한 여러 처리기가 필요한 이유는 무엇 인가요?

평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다. 예를 들어 Microsoft에는 키 카드만 여는 문이 있습니다. 키 카드를 집에서 벗어나면 접수원는 임시 스티커를 인쇄 하 고 도어를 엽니다. 이 시나리오에서는 단일 요구 사항이 *BuildingEntry* 하나는 단일 요구 사항을 검사 합니다.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다. 정책에서을 평가할 때 두 핸들러가 모두 성공 하면 `BuildingEntryRequirement` 정책 평가가 성공 합니다.

## <a name="use-a-func-to-fulfill-a-policy"></a>Func를 사용 하 여 정책 수행

정책을 처리 하는 것이 간단 하 게 코드를 표현할 수 있는 경우가 있을 수 있습니다. `Func<AuthorizationHandlerContext, bool>`정책 작성기를 사용 하 여 정책을 구성할 때를 제공할 수 있습니다 `RequireAssertion` .

예를 들어 이전은 다음과 `BadgeEntryHandler` 같이 다시 작성할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a>처리기의 MVC 요청 컨텍스트 액세스

`HandleRequirementAsync`권한 부여 처리기에서 구현 하는 메서드에는 두 개의 매개 변수인 `AuthorizationHandlerContext` 및를 `TRequirement` 처리 하 고 있습니다. MVC 또는와 같은 프레임 워크 SignalR `Resource` 는 `AuthorizationHandlerContext` 추가 정보를 전달 하기 위해 개체를의 속성에 자유롭게 추가할 수 있습니다.

예를 들어, MVC는 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 `Resource` 합니다. 이 속성은 `HttpContext` `RouteData` MVC 및 페이지에서 제공 되는, 및 모든에 대 한 액세스를 제공 합니다 Razor .

속성을 사용 하는 `Resource` 것은 프레임 워크와 관련이 있습니다. 속성의 정보를 사용 하면 `Resource` 권한 부여 정책이 특정 프레임 워크로 제한 됩니다. `Resource`키워드를 사용 하 여 속성을 캐스팅 한 `is` 다음, `InvalidCastException` 다른 프레임 워크에서 실행 될 때 코드가와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
