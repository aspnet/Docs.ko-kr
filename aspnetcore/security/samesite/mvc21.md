---
title: 'ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 샘플'
author: rick-anderson
description: 'ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 샘플'
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051553"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="fca08-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: 샘플</span><span class="sxs-lookup"><span data-stu-id="fca08-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: sample</span></span>

<span data-ttu-id="fca08-104">ASP.NET Core 2.1는 [SameSite](https://www.owasp.org/index.php/SameSite) 특성에 대 한 기본 제공 지원을 제공 하지만 원래 표준에 기록 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="fca08-105">[패치 된 동작이](https://github.com/dotnet/aspnetcore/issues/8212) 의 의미를 변경 `SameSite.None` 하 여 값을 `None` 전혀 내보내지 않고 sameSite 특성을 값으로 내보냅니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="fca08-106">값을 내보내지 않으려면의 `SameSite` 속성을 :::no-loc(cookie)::: -1로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-106">If you want to not emit the value you can set the `SameSite` property on a :::no-loc(cookie)::: to -1.</span></span>

[!INCLUDE[](~/includes/SameSite:::no-loc(Identity):::.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="fca08-107">SameSite 특성 작성</span><span class="sxs-lookup"><span data-stu-id="fca08-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="fca08-108">다음은에 SameSite 특성을 작성 하는 방법의 예입니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fca08-108">Following is an example of how to write a SameSite attribute on a :::no-loc(cookie)::::</span></span>

```c#
var :::no-loc(cookie):::Options = new :::no-loc(Cookie):::Options
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the :::no-loc(cookie)::: to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the :::no-loc(cookie)::: to the response :::no-loc(cookie)::: collection
Response.:::no-loc(Cookie):::s.Append(:::no-loc(Cookie):::Name, ":::no-loc(cookie):::Value", :::no-loc(cookie):::Options);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="fca08-109">:::no-loc(Cookie):::인증 및 세션 상태 설정 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="fca08-109">Setting :::no-loc(Cookie)::: Authentication and Session State :::no-loc(cookie):::s</span></span>

<span data-ttu-id="fca08-110">:::no-loc(Cookie)::: 인증, 세션 상태 및 [다양 한 기타 구성 요소가](../samesite.md?view=aspnetcore-2.1) 옵션을 통해 sameSite 옵션 :::no-loc(Cookie)::: 을 설정 합니다. 예를 들면</span><span class="sxs-lookup"><span data-stu-id="fca08-110">:::no-loc(Cookie)::: authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via :::no-loc(Cookie)::: options, for example</span></span>

```c#
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
        options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
        options.:::no-loc(Cookie):::.IsEssential = true;
    });

services.AddSession(options =>
{
    options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
    options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
    options.:::no-loc(Cookie):::.IsEssential = true;
});
```

<span data-ttu-id="fca08-111">위의 코드에서 :::no-loc(cookie)::: 인증 및 세션 상태는 모두 sameSite 특성을로 설정 하 `None` 고, 특성을 값으로 내보내고, `None` 보안 특성을 true로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-111">In the preceding code, both :::no-loc(cookie)::: authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="fca08-112">샘플 실행</span><span class="sxs-lookup"><span data-stu-id="fca08-112">Run the sample</span></span>

<span data-ttu-id="fca08-113">[샘플 프로젝트](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)를 실행 하는 경우에는 초기 페이지에서 브라우저 디버거를 로드 하 고이를 사용 하 여 사이트의 컬렉션을 볼 수 있습니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fca08-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the :::no-loc(cookie)::: collection for the site.</span></span> <span data-ttu-id="fca08-114">Edge 및 Chrome에서이 작업을 수행 하려면 `F12` 탭을 선택 하 `Application` 고 섹션의 옵션 아래에서 사이트 URL을 클릭 `:::no-loc(Cookie):::s` `Storage` 합니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `:::no-loc(Cookie):::s` option in the `Storage` section.</span></span>

![Browser Debugger::: no-loc (Cookie)::: List](BrowserDebugger.png)

<span data-ttu-id="fca08-116">위의 이미지에서 볼 수 있습니다. 예를 들어 :::no-loc(cookie)::: "SameSite 만들기" 단추를 클릭 하면 샘플 :::no-loc(Cookie)::: `Lax` [코드](#sampleCode)에 설정 된 값과 일치 하는 SameSite 특성 값이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-116">You can see from the image above that the :::no-loc(cookie)::: created by the sample when you click the "Create SameSite :::no-loc(Cookie):::" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="fca08-117">가로채기 :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="fca08-117">Intercepting :::no-loc(cookie):::s</span></span>

<span data-ttu-id="fca08-118">를 가로채 고 :::no-loc(cookie)::: 사용자의 브라우저 에이전트에서 지원에 따라 없음 값을 조정 하려면 미들웨어를 사용 해야 합니다 `:::no-loc(Cookie):::Policy` .</span><span class="sxs-lookup"><span data-stu-id="fca08-118">In order to intercept :::no-loc(cookie):::s, to adjust the none value according to its support in the user's browser agent you must use the `:::no-loc(Cookie):::Policy` middleware.</span></span> <span data-ttu-id="fca08-119">을 작성 하 고 내에서 구성 된 구성 요소 **보다 먼저** http 요청 파이프라인에 배치 해야 합니다 :::no-loc(cookie)::: `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="fca08-119">This must be placed into the http request pipeline **before** any components that write :::no-loc(cookie):::s and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="fca08-120">`app.Use:::no-loc(Cookie):::Policy()` `Configure(IApplicationBuilder, IHostingEnvironment)` [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)의 메서드에서 파이프라인 사용에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-120">To insert it into the pipeline use `app.Use:::no-loc(Cookie):::Policy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="fca08-121">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-121">For example:</span></span>

```c#
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
    app.Use:::no-loc(Cookie):::Policy();
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

<span data-ttu-id="fca08-122">그런 다음를 `ConfigureServices(IServiceCollection services)` :::no-loc(cookie)::: 추가 하거나 삭제 하면에서 도우미 클래스를 호출 하도록 정책을 구성 합니다 :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="fca08-122">Then in the `ConfigureServices(IServiceCollection services)` configure the :::no-loc(cookie)::: policy to call out to a helper class when :::no-loc(cookie):::s are appended or deleted.</span></span> <span data-ttu-id="fca08-123">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<:::no-loc(Cookie):::PolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppend:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
        options.OnDelete:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
    });
}

private void CheckSameSite(HttpContext httpContext, :::no-loc(Cookie):::Options options)
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

<span data-ttu-id="fca08-124">도우미 함수 `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)` :</span><span class="sxs-lookup"><span data-stu-id="fca08-124">The helper function `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)`:</span></span>

* <span data-ttu-id="fca08-125">는 :::no-loc(cookie)::: 요청에 추가 되거나 요청에서 삭제 될 때 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-125">Is called when :::no-loc(cookie):::s are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="fca08-126">`SameSite`속성이로 설정 되어 있는지 확인 `None` 합니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="fca08-127">`SameSite`가로 설정 되어 `None` 있고 현재 사용자 에이전트가 none 특성 값을 지원 하지 않는 것으로 알려진 경우</span><span class="sxs-lookup"><span data-stu-id="fca08-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="fca08-128">확인은 [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) 클래스를 사용 하 여 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="fca08-129">`SameSite`속성을로 설정 하 여 값을 내보내지 않도록 설정 합니다.`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="fca08-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="fca08-130">대상 지정 .NET Framework</span><span class="sxs-lookup"><span data-stu-id="fca08-130">Targeting .NET Framework</span></span>

<span data-ttu-id="fca08-131">ASP.NET Core 및 System.web (ASP.NET 클래식)에는 SameSite의 독립적인 구현이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="fca08-132">.NET Framework에 대 한 SameSite KB 패치는 ASP.NET Core를 사용 하는 경우에는 필요 하지 않으며 SameSite에 대 한 최소 프레임 워크 버전 요구 사항 (.NET 4.7.2)이 ASP.NET Core에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="fca08-133">.NET의 ASP.NET Core 적절 한 수정을 얻으려면 nuget 패키지 종속성을 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="fca08-134">.NET Framework에 대 한 ASP.NET Core 변경 내용을 얻으려면 패치 된 패키지 및 버전 (2.1.14 이상 2.1 버전)에 대 한 직접 참조를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fca08-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.:::no-loc(Cookie):::Policy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="fca08-135">추가 정보</span><span class="sxs-lookup"><span data-stu-id="fca08-135">More Information</span></span>
 
<span data-ttu-id="fca08-136">[Chrome 업데이트](https://www.chromium.org/updates/same-site) 
 [ASP.NET Core SameSite 설명서](../samesite.md?view=aspnetcore-2.1) 
 [ASP.NET Core 2.1 SameSite 변경 공지](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="fca08-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>