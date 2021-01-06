---
title: 지역화 확장성
author: hishamco
description: ASP.NET Core 앱에서 지역화 API를 확장하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
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
uid: fundamentals/localization-extensibility
ms.openlocfilehash: a6ef5a547e6ccba6771cdf892a9636f83d6796b1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053737"
---
# <a name="localization-extensibility"></a>지역화 확장성

작성자: [Hisham Bin Ateya](https://github.com/hishamco)

이 문서의 내용:

* 지역화 API의 확장 지점이 나열됩니다.
* ASP.NET Core 앱 지역화를 확장하는 방법에 대한 지침을 제공합니다.

## <a name="extensible-points-in-localization-apis"></a>지역화 API의 확장점

ASP.NET Core 지역화 API는 확장 가능하도록 빌드됩니다. 확장성을 통해 개발자는 필요에 따라 지역화를 사용자 지정할 수 있습니다. 예를 들어 [OrchardCore](https://github.com/orchardCMS/OrchardCore/)에는 `POStringLocalizer`가 있습니다. `POStringLocalizer`에서는 `PO` 파일을 사용하여 지역화 리소스를 저장하는 데 [이식 가능 개체 지역화](xref:fundamentals/portable-object-localization)를 사용하는 데 관한 자세한 내용을 설명합니다.

이 문서에는 지역화 API에서 제공하는 두 가지 주요 확장 지점이 나열되어 있습니다. 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>지역화 문화권 공급자

ASP.NET Core 지역화 API에는 실행 요청의 현재 문화권을 확인할 수 있는 네 가지 기본 공급자가 있습니다.

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

위의 공급자는 [지역화 미들웨어](xref:fundamentals/localization) 문서에 자세히 설명되어 있습니다. 기본 공급자가 요구 사항을 충족하지 않으면 다음 방법 중 하나를 사용하여 사용자 지정 공급자를 빌드합니다.

### <a name="use-customrequestcultureprovider"></a>CustomRequestCultureProvider 사용

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>에서는 단순 대리자를 사용하여 현재 지역화 문화를 판별하는 사용자 지정 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>를 제공합니다.

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>RequestCultureProvider의 새 구현 사용

사용자 지정 소스에서 요청 문화 정보를 판별하는 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>의 새 구현을 만들 수 있습니다. 예를 들어 사용자 지정 소스는 구성 파일 또는 데이터베이스일 수 있습니다.

다음 예제는 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>를 확장하여 *appsettings.json* 에서 요청 문화권 정보를 확인하는 `AppSettingsRequestCultureProvider`를 보여 줍니다.

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a>지역화 리소스

ASP.NET Core 지역화에서는 <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>를 제공합니다. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>는 `resx`를 사용하여 지역화 리소스를 저장하는 <xref:Microsoft.Extensions.Localization.IStringLocalizer>의 구현입니다.

`resx` 파일만 사용하도록 제한되지 않습니다. `IStringLocalized`를 구현하여 모든 데이터 소스를 사용할 수 있습니다.

다음 예제 프로젝트에서는 <xref:Microsoft.Extensions.Localization.IStringLocalizer>를 구현합니다. 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
