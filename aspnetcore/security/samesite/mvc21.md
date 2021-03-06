---
title: ASP.NET Core 2.1 MVC SameSite cookie 샘플
author: rick-anderson
description: ASP.NET Core 2.1 MVC SameSite cookie 샘플
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: a7b7ce0d0fff2953097f29a2b6d2c5a539cac3a5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586529"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>ASP.NET Core 2.1 MVC SameSite cookie 샘플

ASP.NET Core 2.1는 [SameSite](https://www.owasp.org/index.php/SameSite) 특성에 대 한 기본 제공 지원을 제공 하지만 원래 표준에 기록 되었습니다. [패치 된 동작이](https://github.com/dotnet/aspnetcore/issues/8212) 의 의미를 변경 `SameSite.None` 하 여 값을 `None` 전혀 내보내지 않고 sameSite 특성을 값으로 내보냅니다. 값을 내보내지 않으려면의 `SameSite` 속성을 cookie -1로 설정할 수 있습니다.

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a>SameSite 특성 작성

다음은에 SameSite 특성을 작성 하는 방법의 예입니다 cookie .

```csharp
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>Cookie인증 및 세션 상태 설정 cookie

Cookie 인증, 세션 상태 및 [다양 한 기타 구성 요소가](../samesite.md?view=aspnetcore-2.1) 옵션을 통해 sameSite 옵션 Cookie 을 설정 합니다. 예를 들면

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

위의 코드에서 cookie 인증 및 세션 상태는 모두 sameSite 특성을로 설정 하 `None` 고, 특성을 값으로 내보내고, `None` 보안 특성을 true로 설정 합니다.

### <a name="run-the-sample"></a>샘플 실행

[샘플 프로젝트](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)를 실행 하는 경우에는 초기 페이지에서 브라우저 디버거를 로드 하 고이를 사용 하 여 사이트의 컬렉션을 볼 수 있습니다 cookie . Edge 및 Chrome에서이 작업을 수행 하려면 `F12` 탭을 선택 하 `Application` 고 섹션의 옵션 아래에서 사이트 URL을 클릭 `Cookies` `Storage` 합니다.

![Browser Debugger::: no-loc (Cookie)::: List](BrowserDebugger.png)

위의 이미지에서 볼 수 있습니다. 예를 들어 cookie "SameSite 만들기" 단추를 클릭 하면 샘플 Cookie `Lax` [코드](#sampleCode)에 설정 된 값과 일치 하는 SameSite 특성 값이 있습니다.

## <a name="intercepting-cookies"></a><a name="interception"></a>가로채기 cookie

를 가로채 고 cookie 사용자의 브라우저 에이전트에서 지원에 따라 없음 값을 조정 하려면 미들웨어를 사용 해야 합니다 `CookiePolicy` . 을 작성 하 고 내에서 구성 된 구성 요소 **보다 먼저** http 요청 파이프라인에 배치 해야 합니다 cookie `ConfigureServices()` .

`app.UseCookiePolicy()` `Configure(IApplicationBuilder, IHostingEnvironment)` [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)의 메서드에서 파이프라인 사용에 삽입 합니다. 예를 들면 다음과 같습니다.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

그런 다음를 `ConfigureServices(IServiceCollection services)` cookie 추가 하거나 삭제 하면에서 도우미 클래스를 호출 하도록 정책을 구성 합니다 cookie . 예를 들면 다음과 같습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

도우미 함수 `CheckSameSite(HttpContext, CookieOptions)` :

* 는 cookie 요청에 추가 되거나 요청에서 삭제 될 때 호출 됩니다.
* `SameSite`속성이로 설정 되어 있는지 확인 `None` 합니다.
* `SameSite`가로 설정 되어 `None` 있고 현재 사용자 에이전트가 none 특성 값을 지원 하지 않는 것으로 알려진 경우 확인은 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) 클래스를 사용 하 여 수행 됩니다.
  * `SameSite`속성을로 설정 하 여 값을 내보내지 않도록 설정 합니다.`(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>대상 지정 .NET Framework

ASP.NET Core 및 System.web (ASP.NET 4.x)에는 SameSite의 독립적인 구현이 있습니다. .NET Framework에 대 한 SameSite KB 패치는 ASP.NET Core를 사용 하는 경우 필요 하지 않으며 SameSite 4.7.2 .NET Framework (최소 프레임 워크 버전 요구 사항)이 ASP.NET Core에 적용 됩니다.

.NET의 ASP.NET Core 적절 한 수정을 얻으려면 NuGet 패키지 종속성을 업데이트 해야 합니다.

.NET Framework에 대 한 ASP.NET Core 변경 내용을 얻으려면 패치 된 패키지 및 버전 (2.1.14 이상 2.1 버전)에 대 한 직접 참조를 확인 합니다.

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>추가 정보
 
[Chrome 업데이트](https://www.chromium.org/updates/same-site) 
 [ASP.NET Core SameSite 설명서](../samesite.md?view=aspnetcore-2.1) 
 [ASP.NET Core 2.1 SameSite 변경 공지](https://github.com/dotnet/aspnetcore/issues/8212)