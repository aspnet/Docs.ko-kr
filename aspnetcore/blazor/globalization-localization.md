---
title: ASP.NET Core Blazor 세계화 및 지역화
author: guardrex
description: 여러 문화권과 언어의 사용자가 Razor 구성 요소에 액세스할 수 있도록 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 8baee9bc0a8e569174f33dac6a406b2162d09552
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279912"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a>ASP.NET Core Blazor 세계화 및 지역화

여러 문화권과 언어의 사용자가 Razor 구성 요소에 액세스할 수 있습니다. 다음 .NET 세계화 및 지역화 시나리오를 사용할 수 있습니다.

* . NET의 리소스 시스템
* 문화권별 숫자 및 날짜 형식 지정

다음과 같은 제한된 ASP.NET Core의 지역화 시나리오가 현재 지원됩니다.

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 Blazor 앱에서 지원됩니다.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> 및 데이터 주석 지역화는 ASP.NET Core MVC 시나리오로, Blazor 앱에서 **지원되지 않습니다**.

자세한 내용은 <xref:fundamentals/localization>를 참조하세요.

## <a name="globalization"></a>전역화

Blazor의 [`@bind`](xref:mvc/views/razor#bind) 기능은 사용자의 현재 문화권에 따라 표시하기 위해 형식을 지정하고 값을 구문 분석합니다.

현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>는 다음 필드 형식(`<input type="{TYPE}" />`)에 사용됩니다.

* `date`
* `number`

이전 필드 형식:

* 적절한 브라우저 기반 서식 지정 규칙을 사용하여 표시됩니다.
* 자유 형식 텍스트를 포함할 수 없습니다.
* 브라우저의 구현에 따라 사용자 상호 작용 특성을 제공합니다.

다음 필드 형식은 특정 형식 지정 요구 사항이 있으며, 모든 주요 브라우저에서 지원되는 것은 아니므로 현재 Blazor에서 지원되지 않습니다.

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind)는 `@bind:culture` 매개 변수를 지원하여 값을 구문 분석하고 서식을 지정하기 위한 <xref:System.Globalization.CultureInfo?displayProperty=fullName>을 제공합니다. `date` 및 `number` 필드 형식을 사용할 때는 문화권을 지정하지 않는 것이 좋습니다. `date` 및 `number`는 필수 문화권을 제공하는 Blazor가 기본적으로 제공되어 있습니다.

## <a name="localization"></a>지역화

### Blazor WebAssembly

Blazor WebAssembly 앱은 사용자의 [언어 기본 설정](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)을 사용하여 문화권을 설정합니다.

문화권을 명시적으로 구성하려면 `Program.Main`에서 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType>와 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType>를 설정하세요.

::: moniker range=">= aspnetcore-5.0"

기본적으로 Blazor WebAssembly는 날짜 및 통화와 같은 값을 사용자의 문화권에 표시하는 데 필요한 최소한의 세계화 리소스를 전달합니다. 문화권 변경을 동적으로 지원해야 하는 애플리케이션은 프로젝트 파일에서 `BlazorWebAssemblyLoadAllGlobalizationData`를 구성해야 합니다.

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

Blazor WebAssembly는 `Blazor.start`에 전달된 옵션을 사용하여 특정 애플리케이션 문화권으로 실행하도록 구성할 수도 있습니다. 예를 들어 아래 샘플은 `en-GB` 문화권으로 실행하도록 구성된 앱을 보여줍니다.

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

`applicationCulture`의 값은 [BCP-47 언어 태그 형식](https://tools.ietf.org/html/bcp47)을 준수해야 합니다.

앱에 지역화가 필요하지 않은 경우 `en-US` 문화권을 기반으로 하는 고정 문화권을 지원하도록 앱을 구성할 수 있습니다.

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

기본적으로 Blazor WebAssembly 앱에 대한 IL(중간 언어) 링커 구성에서는 명시적으로 요청된 로캘을 제외한 국제화 정보를 제거합니다. 자세한 내용은 <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>를 참조하세요.

::: moniker-end

Blazor가 선택하는 문화권은 대부분의 사용자에게 충분할 수 있지만 사용자가 원하는 로캘을 지정할 방법을 제공하는 방안도 고려하세요. 문화권 선택기가 포함된 Blazor WebAssembly 샘플 앱을 살펴보려면 [`LocSample`](https://github.com/pranavkm/LocSample) 지역화 샘플 앱을 참조하세요.

### Blazor Server

Blazor Server 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용하여 지역화됩니다. 미들웨어는 앱에서 리소스를 요청하는 사용자에게 적절한 문화권을 선택합니다.

문화권은 다음 방법 중 하나를 사용하여 설정할 수 있습니다.

* [Cookie](#cookies)
* [문화권을 선택하기 위한 UI 제공](#provide-ui-to-choose-the-culture)

자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.

#### <a name="cookies"></a>Cookies

지역화 문화권 cookie는 사용자의 문화권을 유지할 수 있습니다. 지역화 미들웨어는 후속 요청에서 cookie를 읽어 사용자의 문화권을 설정합니다. 

cookie를 사용하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있게 됩니다. 지역화 체계가 URL 경로 또는 쿼리 문자열을 기준으로 하는 경우 스키마는 WebSocket을 사용하지 못할 수 있으므로 문화권이 유지되지 않습니다. 따라서 지역화 문화권 cookie를 사용하는 것이 좋습니다.

문화권이 지역화 cookie에 유지되는 경우 임의 기술을 사용하여 문화권을 할당할 수 있습니다. 앱에 서버 쪽 ASP.NET Core에 대해 설정된 지역화 체계가 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용하고 앱의 체계 내에서 지역화 문화권 cookie를 설정합니다.

다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 cookie의 현재 문화권을 설정하는 방법을 보여 줍니다. `Pages/_Host.cshtml` 파일에서 여는 `<body>` 태그 바로 안에 Razor 식을 만듭니다.

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

지역화는 다음과 같은 이벤트 시퀀스에 따라 앱에서 처리됩니다.

1. 브라우저가 앱에 초기 HTTP 요청을 보냅니다.
1. 문화권은 지역화 미들웨어에 의해 할당됩니다.
1. `_Host` 페이지(`_Host.cshtml`)의 Razor 식은 응답의 일부로 cookie에 문화권을 유지합니다.
1. 브라우저는 WebSocket 연결을 열어 대화형 Blazor Server 세션을 만듭니다.
1. 지역화 미들웨어는 cookie를 읽고 문화권을 할당합니다.
1. Blazor Server 세션이 올바른 문화권으로 시작합니다.

<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage>로 작업하는 경우 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> 속성을 사용합니다.

```razor
@{
    this.Context.Response.Cookies.Append(
        CookieRequestCultureProvider.DefaultCookieName,
        CookieRequestCultureProvider.MakeCookieValue(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a>문화권을 선택하기 위한 UI 제공

사용자가 문화권을 선택하기 위한 UI를 제공하려면 *리디렉션 기반 접근 방식* 이 권장됩니다. 이 프로세스는 사용자가 보안 리소스에 액세스하려고 할 때 웹앱에서 발생하는 작업과 비슷합니다. 즉, 사용자는 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션됩니다. 

앱은 컨트롤러에 대한 리디렉션을 통해 사용자가 선택한 문화권을 유지합니다. 컨트롤러는 사용자가 선택한 문화권을 cookie로 설정하고 사용자를 원래 URI로 다시 리디렉션합니다.

서버에서 HTTP 엔드포인트를 설정하여 사용자가 선택한 문화권을 cookie에 설정하고 다시 원래 URI로 리디렉션합니다.

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> 작업 결과를 사용하여 오픈 리디렉션 공격을 방지합니다. 자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.

앱이 컨트롤러 작업을 처리하도록 구성되지 않은 경우 다음과 같습니다.

* `Startup.ConfigureServices`의 서비스 컬렉션에 MVC 서비스를 추가합니다.

  ```csharp
  services.AddControllers();
  ```

* `Startup.Configure`에서 컨트롤러 엔드포인트 라우팅을 추가합니다.

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행하는 방법의 예를 보여 줍니다.

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/localization>
