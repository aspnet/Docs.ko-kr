---
title: cookieASP.NET apps 간에 인증 공유
author: rick-anderson
description: cookieASP.NET 4.x 및 ASP.NET Core apps 간의 인증을 공유 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252996"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>cookieASP.NET apps 간에 인증 공유

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

Websites는 일반적으로 함께 작동 하는 개별 웹 앱으로 구성 됩니다. SSO (Single Sign-On) 환경을 제공 하려면 사이트 내의 웹 앱이 인증을 공유 해야 합니다 cookie . 이 시나리오를 지원 하기 위해 데이터 보호 스택에서 Katana cookie 인증 및 ASP.NET Core 인증 티켓을 공유할 수 있습니다 cookie .

다음 예제에서:

* 인증 cookie 이름이의 일반 값으로 설정 됩니다 `.AspNet.SharedCookie` .
* 는 `AuthenticationType` 명시적으로 또는 기본적으로로 설정 됩니다 `Identity.Application` .
* 공통 앱 이름은 데이터 보호 시스템에서 데이터 보호 키 ()를 공유 하도록 설정 하는 데 사용 됩니다 `SharedCookieApp` .
* `Identity.Application` 는 인증 체계로 사용 됩니다. 사용 되는 스키마는 모두 공유 된 앱 *내에서* 일관 되 게 사용 되어야 하며, cookie 기본 체계로 사용 하거나 명시적으로 설정 하 여 사용 해야 합니다. 체계는를 암호화 하 고 암호를 해독 하는 데 사용 cookie 되므로 앱 간에 일관 된 스키마를 사용 해야 합니다.
* 일반적인 [데이터 보호 키](xref:security/data-protection/implementation/key-management) 저장소 위치가 사용 됩니다.
  * ASP.NET Core 앱에서 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 는 키 저장소 위치를 설정 하는 데 사용 됩니다.
  * .NET Framework 앱에서 Cookie 인증 미들웨어는의 구현을 사용 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 합니다. `DataProtectionProvider` 는 인증 페이로드 데이터의 암호화 및 암호 해독을 위한 데이터 보호 서비스를 제공 합니다 cookie . `DataProtectionProvider`인스턴스는 응용 프로그램의 다른 부분에서 사용 되는 데이터 보호 시스템에서 격리 됩니다. [DataProtectionProvider (system.io.directoryinfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 은를 허용 <xref:System.IO.DirectoryInfo> 하 여 데이터 보호 키 저장소의 위치를 지정 합니다.
* `DataProtectionProvider`[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet 패키지가 필요 합니다.
  * ASP.NET Core 2.x 앱에서 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조 합니다.
  * .NET Framework apps에서 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)에 대 한 패키지 참조를 추가 합니다.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 공통 앱 이름을 설정 합니다.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>cookie인증 공유ASP.NET Core Identity

ASP.NET Core Identity를 사용하는 경우:

* 데이터 보호 키와 앱 이름은 앱 간에 공유 되어야 합니다. 다음 예에서는 일반 키 저장소 위치가 메서드에 제공 됩니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> . <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>다음 예에서는를 사용 하 여 공통 공유 앱 이름을 구성 `SharedCookieApp` 합니다. 자세한 내용은 <xref:security/data-protection/configuration/overview>를 참조하세요.
* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>확장 메서드를 사용 하 여에 대 한 데이터 보호 서비스를 설정 cookie 합니다.
* 기본 인증 형식은 `Identity.Application` 입니다.

`Startup.ConfigureServices`의 경우

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

**참고:** 위의 지침은 ()에서 작동 하지 않습니다 `ITicketStore` `CookieAuthenticationOptions.SessionStore` .  자세한 내용은 [이 GitHub 이슈](https://github.com/dotnet/AspNetCore.Docs/issues/21163)를 참조하세요.

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>다음을 사용 cookie 하지 않고 인증 공유 ASP.NET Core Identity

을 (를 cookie ) 사용 하지 않고을 직접 사용 하 ASP.NET Core Identity 는 경우에서 데이터 보호 및 인증을 구성 `Startup.ConfigureServices` 합니다. 다음 예제에서는 인증 형식이로 설정 됩니다 `Identity.Application` .

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a>cookie여러 기본 경로에서 s 공유

인증은 cookie [HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 을 기본값으로 사용 [ Cookie 합니다. 경로](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). 앱 cookie 을 다른 기본 경로에서 공유 해야 하는 경우에는를 `Path` 재정의 해야 합니다.

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>하위 cookie 도메인 간 공유

하위 도메인에서를 공유 하는 앱을 호스트 하는 경우 cookie 에서 공통 도메인을 지정 [ Cookie 합니다. 도메인](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) 속성입니다. 및와 cookie 같이에서 앱 간에를 공유 하려면를 `contoso.com` 다음과 같이 `first_subdomain.contoso.com` `second_subdomain.contoso.com` 지정 `Cookie.Domain` 합니다 `.contoso.com` .

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>휴지 상태의 데이터 보호 키 암호화

프로덕션 배포의 경우 `DataProtectionProvider` DPAPI 또는 X509Certificate를 사용 하 여 미사용 키를 암호화 하도록를 구성 합니다. 자세한 내용은 <xref:security/data-protection/implementation/key-encryption-at-rest>를 참조하세요. 다음 예제에서는에 대 한 인증서 지문을 제공 합니다 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> .

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>ASP.NET 4.x cookie 와 ASP.NET Core apps 간에 인증 공유

Katana 인증 미들웨어를 사용 하는 ASP.NET 4. x 앱은 Cookie cookie ASP.NET Core 인증 미들웨어와 호환 되는 인증을 생성 하도록 구성할 수 있습니다 Cookie . 이를 통해 여러 단계로 많은 사이트의 개별 앱을 업그레이드할 수 있으며 사이트 전체에서 원활한 SSO 환경을 제공 합니다.

앱에서 Katana Cookie 인증 미들웨어를 사용 하는 경우 `UseCookieAuthentication` 프로젝트의 *Startup.Auth.cs* 파일에서를 호출 합니다. Visual Studio 2013 이상에서 만든 ASP.NET 4.x 웹 앱 프로젝트는 Cookie 기본적으로 Katana Authentication 미들웨어를 사용 합니다. `UseCookieAuthentication`는 ASP.NET Core apps에서 사용 되지 않으며 지원 되지 않지만 `UseCookieAuthentication` Katana 인증 미들웨어를 사용 하는 ASP.NET 4.x 앱에서 호출은 Cookie 유효 합니다.

ASP.NET 4.x 앱은 4.5.1 이상 .NET Framework 대상으로 해야 합니다. 그렇지 않으면 필요한 NuGet 패키지를 설치 하지 못합니다.

cookieASP.NET 4.x 앱과 ASP.NET Core 앱 간의 인증을 공유 하려면 [ cookie ASP.NET Core 앱 간 인증 공유](#share-authentication-cookies-with-aspnet-core-identity) 섹션에 설명 된 대로 ASP.NET Core 앱을 구성 하 고 다음과 같이 ASP.NET 4.x 앱을 구성 합니다.

앱의 패키지가 최신 릴리스로 업데이트 되는지 확인 합니다. 각 ASP.NET 4.x 앱에 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 패키지를 설치 합니다.

호출을 찾아서 수정 합니다 `UseCookieAuthentication` .

* cookieASP.NET Core 인증 미들웨어에서 사용 하는 이름과 일치 하도록 이름을 변경 Cookie 합니다 ( `.AspNet.SharedCookie` 예제에서는).
* 다음 예제에서는 인증 형식이로 설정 됩니다 `Identity.Application` .
* `DataProtectionProvider`공통 데이터 보호 키 저장소 위치에 초기화 된의 인스턴스를 제공 합니다.
* 앱 이름이 인증을 공유 하는 모든 앱에서 사용 하는 공용 앱 이름으로 설정 되어 있는지 확인 cookie 합니다 (이 `SharedCookieApp` 예제에서는).

및를 설정 하지 않으면 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 고유한 사용자를 구별 하는 클레임으로 설정 합니다.

*App_Start/startup.auth.cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

사용자 id를 생성 하는 경우 인증 유형 ( `Identity.Application` )이 `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs* 의로 설정 된에 정의 된 유형과 일치 해야 합니다.

*모델/ Identity Models.cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>공용 사용자 데이터베이스 사용

앱에서 동일한 Identity 스키마 (동일한 버전의)를 사용 하는 경우 Identity Identity 각 앱의 시스템이 동일한 사용자 데이터베이스를 가리키고 있는지 확인 합니다. 그렇지 않으면 id 시스템은 인증 정보를 데이터베이스의 정보에 대해 일치 시 키 려 고 할 때 런타임에 오류를 생성 합니다 cookie .

앱에서 Identity 스키마가 서로 다를 때, 일반적으로 앱이 서로 다른 버전을 사용 하기 때문에 Identity 최신 버전을 기반으로 하는 공통 데이터베이스를 공유 하는 것은 Identity 다른 앱의 스키마에서 열을 다시 매핑 및 추가 하지 않고도 가능 Identity 합니다 Identity앱에서 공통 데이터베이스를 공유할 수 있도록 최신 버전을 사용 하도록 다른 앱을 업그레이드 하는 것이 더 효율적입니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:host-and-deploy/web-farm>
