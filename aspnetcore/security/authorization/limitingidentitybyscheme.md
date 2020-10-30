---
title: ASP.NET Core에서 특정 체계를 사용 하 여 권한 부여
author: rick-anderson
description: 이 문서에서는 여러 인증 방법으로 작업할 때 id를 특정 체계로 제한 하는 방법을 설명 합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 4dc86480d40d8ee40b3c03aa7fd2994e6c15b105
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053126"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>ASP.NET Core에서 특정 체계를 사용 하 여 권한 부여

SPAs (단일 페이지 응용 프로그램)와 같은 일부 시나리오에서는 여러 인증 방법을 사용 하는 것이 일반적입니다. 예를 들어 앱은 cookie 기반 인증을 사용 하 여 JavaScript 요청에 대 한 JWT 전달자 인증 및 로그인을 사용할 수 있습니다. 경우에 따라 앱에 인증 처리기의 인스턴스가 여러 개 있을 수 있습니다. 예를 들어, cookie 하나는 기본 id를 포함 하 고 다른 하나는 MFA (multi-factor authentication)가 트리거될 때 생성 되는 두 개의 처리기입니다. 사용자가 추가 보안이 필요한 작업을 요청 하 여 MFA를 트리거할 수 있습니다. 사용자가 MFA를 요구 하는 리소스를 요청할 때 MFA를 적용 하는 방법에 대 한 자세한 내용은 MFA를 사용 하 여 GitHub 문제 [보호 섹션](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)을 참조 하세요.

인증 체계는 인증 중에 인증 서비스가 구성 될 때 이름이 지정 됩니다. 다음은 그 예입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

위의 코드에서 두 개의 인증 처리기가 추가 되었습니다. 하나는 하나이 cookie 고 다른 하나는 전달자 용입니다.

>[!NOTE]
>기본 스키마를 지정 하면 `HttpContext.User` 속성이 해당 id로 설정 됩니다. 해당 동작이 필요 하지 않은 경우에는 매개 변수가 없는 형식을 호출 하 여 사용 하지 않도록 설정 `AddAuthentication` 합니다.

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>권한 부여 특성이 있는 체계 선택

권한 부여 시점에서 앱은 사용할 처리기를 나타냅니다. 쉼표로 구분 된 인증 체계 목록을에 전달 하 여 앱에 권한을 부여 하는 처리기를 선택 `[Authorize]` 합니다. `[Authorize]`특성은 기본값이 구성 되어 있는지 여부에 관계 없이 사용할 인증 체계 또는 체계를 지정 합니다. 다음은 그 예입니다.

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

앞의 예제에서는 cookie 및 전달자 처리기를 실행 하 고 현재 사용자에 대 한 id를 만들고 추가할 수 있습니다. 단일 스키마만 지정 하면 해당 처리기가 실행 됩니다.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

위의 코드에서는 "전달자" 체계가 있는 처리기만 실행 됩니다. 모든 cookie 기반 id는 무시 됩니다.

## <a name="selecting-the-scheme-with-policies"></a>정책을 사용 하 여 체계 선택

[정책](xref:security/authorization/policies)에서 원하는 스키마를 지정 하려는 경우 `AuthenticationSchemes` 정책을 추가할 때 컬렉션을 설정할 수 있습니다.

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

위의 예제에서 "Over18" 정책은 "전달자" 처리기에서 만든 id에 대해서만 실행 됩니다. 특성의 속성을 설정 하 여 정책을 사용 합니다 `[Authorize]` `Policy` .

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>여러 인증 체계 사용

일부 앱은 여러 유형의 인증을 지원 해야 할 수 있습니다. 예를 들어 앱이 사용자 데이터베이스에서 Azure Active Directory 사용자를 인증할 수 있습니다. 또 다른 예로 Active Directory Federation Services 및 Azure Active Directory B2C에서 사용자를 인증 하는 앱이 있습니다. 이 경우 앱은 여러 발급자의 JWT 전달자 토큰을 수락 해야 합니다.

수락 하려는 모든 인증 스키마를 추가 합니다. 예를 들어의 다음 코드는 `Startup.ConfigureServices` 발급자가 서로 다른 두 개의 JWT 전달자 인증 체계를 추가 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> 기본 인증 체계에는 하나의 JWT 전달자 인증만 등록 됩니다 `JwtBearerDefaults.AuthenticationScheme` . 추가 인증은 고유한 인증 체계를 사용 하 여 등록 해야 합니다.

다음 단계는 두 인증 체계를 모두 허용 하도록 기본 권한 부여 정책을 업데이트 하는 것입니다. 다음은 그 예입니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

기본 권한 부여 정책이 재정의 되 면 `[Authorize]` 컨트롤러에서 특성을 사용할 수 있습니다. 그러면 컨트롤러는 첫 번째 또는 두 번째 발급자가 발급 한 JWT를 사용 하 여 요청을 수락 합니다.

::: moniker-end
