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
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="3345b-103">ASP.NET Core Blazor 세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="3345b-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="3345b-104">여러 문화권과 언어의 사용자가 Razor 구성 요소에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-104">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="3345b-105">다음 .NET 세계화 및 지역화 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-105">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="3345b-106">. NET의 리소스 시스템</span><span class="sxs-lookup"><span data-stu-id="3345b-106">.NET's resources system</span></span>
* <span data-ttu-id="3345b-107">문화권별 숫자 및 날짜 형식 지정</span><span class="sxs-lookup"><span data-stu-id="3345b-107">Culture-specific number and date formatting</span></span>

<span data-ttu-id="3345b-108">다음과 같은 제한된 ASP.NET Core의 지역화 시나리오가 현재 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-108">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="3345b-109"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 Blazor 앱에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-109"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="3345b-110"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> 및 데이터 주석 지역화는 ASP.NET Core MVC 시나리오로, Blazor 앱에서 **지원되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="3345b-110"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="3345b-111">자세한 내용은 <xref:fundamentals/localization>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3345b-111">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="3345b-112">전역화</span><span class="sxs-lookup"><span data-stu-id="3345b-112">Globalization</span></span>

<span data-ttu-id="3345b-113">Blazor의 [`@bind`](xref:mvc/views/razor#bind) 기능은 사용자의 현재 문화권에 따라 표시하기 위해 형식을 지정하고 값을 구문 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-113">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="3345b-114">현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-114">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="3345b-115"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>는 다음 필드 형식(`<input type="{TYPE}" />`)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-115"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="3345b-116">이전 필드 형식:</span><span class="sxs-lookup"><span data-stu-id="3345b-116">The preceding field types:</span></span>

* <span data-ttu-id="3345b-117">적절한 브라우저 기반 서식 지정 규칙을 사용하여 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-117">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="3345b-118">자유 형식 텍스트를 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-118">Can't contain free-form text.</span></span>
* <span data-ttu-id="3345b-119">브라우저의 구현에 따라 사용자 상호 작용 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-119">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="3345b-120">다음 필드 형식은 특정 형식 지정 요구 사항이 있으며, 모든 주요 브라우저에서 지원되는 것은 아니므로 현재 Blazor에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-120">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="3345b-121">[`@bind`](xref:mvc/views/razor#bind)는 `@bind:culture` 매개 변수를 지원하여 값을 구문 분석하고 서식을 지정하기 위한 <xref:System.Globalization.CultureInfo?displayProperty=fullName>을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-121">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="3345b-122">`date` 및 `number` 필드 형식을 사용할 때는 문화권을 지정하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-122">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="3345b-123">`date` 및 `number`는 필수 문화권을 제공하는 Blazor가 기본적으로 제공되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-123">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="3345b-124">지역화</span><span class="sxs-lookup"><span data-stu-id="3345b-124">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="3345b-125">Blazor WebAssembly 앱은 사용자의 [언어 기본 설정](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)을 사용하여 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-125">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="3345b-126">문화권을 명시적으로 구성하려면 `Program.Main`에서 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType>와 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType>를 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="3345b-126">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3345b-127">기본적으로 Blazor WebAssembly는 날짜 및 통화와 같은 값을 사용자의 문화권에 표시하는 데 필요한 최소한의 세계화 리소스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-127">By default, Blazor WebAssembly carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="3345b-128">문화권 변경을 동적으로 지원해야 하는 애플리케이션은 프로젝트 파일에서 `BlazorWebAssemblyLoadAllGlobalizationData`를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-128">Applications that must support dynamically changing the culture should configure `BlazorWebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="3345b-129">Blazor WebAssembly는 `Blazor.start`에 전달된 옵션을 사용하여 특정 애플리케이션 문화권으로 실행하도록 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-129">Blazor WebAssembly can also be configured to launch using a specific application culture using options passed to `Blazor.start`.</span></span> <span data-ttu-id="3345b-130">예를 들어 아래 샘플은 `en-GB` 문화권으로 실행하도록 구성된 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-130">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="3345b-131">`applicationCulture`의 값은 [BCP-47 언어 태그 형식](https://tools.ietf.org/html/bcp47)을 준수해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-131">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="3345b-132">앱에 지역화가 필요하지 않은 경우 `en-US` 문화권을 기반으로 하는 고정 문화권을 지원하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-132">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3345b-133">기본적으로 Blazor WebAssembly 앱에 대한 IL(중간 언어) 링커 구성에서는 명시적으로 요청된 로캘을 제외한 국제화 정보를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-133">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="3345b-134">자세한 내용은 <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3345b-134">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="3345b-135">Blazor가 선택하는 문화권은 대부분의 사용자에게 충분할 수 있지만 사용자가 원하는 로캘을 지정할 방법을 제공하는 방안도 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="3345b-135">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="3345b-136">문화권 선택기가 포함된 Blazor WebAssembly 샘플 앱을 살펴보려면 [`LocSample`](https://github.com/pranavkm/LocSample) 지역화 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3345b-136">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="3345b-137">Blazor Server 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용하여 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-137">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="3345b-138">미들웨어는 앱에서 리소스를 요청하는 사용자에게 적절한 문화권을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-138">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="3345b-139">문화권은 다음 방법 중 하나를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-139">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="3345b-140">[Cookie](#cookies)</span><span class="sxs-lookup"><span data-stu-id="3345b-140">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="3345b-141">문화권을 선택하기 위한 UI 제공</span><span class="sxs-lookup"><span data-stu-id="3345b-141">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="3345b-142">자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="3345b-142">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="3345b-143">Cookies</span><span class="sxs-lookup"><span data-stu-id="3345b-143">Cookies</span></span>

<span data-ttu-id="3345b-144">지역화 문화권 cookie는 사용자의 문화권을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-144">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="3345b-145">지역화 미들웨어는 후속 요청에서 cookie를 읽어 사용자의 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-145">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="3345b-146">cookie를 사용하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-146">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="3345b-147">지역화 체계가 URL 경로 또는 쿼리 문자열을 기준으로 하는 경우 스키마는 WebSocket을 사용하지 못할 수 있으므로 문화권이 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-147">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="3345b-148">따라서 지역화 문화권 cookie를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-148">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="3345b-149">문화권이 지역화 cookie에 유지되는 경우 임의 기술을 사용하여 문화권을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-149">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="3345b-150">앱에 서버 쪽 ASP.NET Core에 대해 설정된 지역화 체계가 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용하고 앱의 체계 내에서 지역화 문화권 cookie를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-150">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="3345b-151">다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 cookie의 현재 문화권을 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-151">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="3345b-152">`Pages/_Host.cshtml` 파일에서 여는 `<body>` 태그 바로 안에 Razor 식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-152">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

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

<span data-ttu-id="3345b-153">지역화는 다음과 같은 이벤트 시퀀스에 따라 앱에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-153">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="3345b-154">브라우저가 앱에 초기 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-154">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="3345b-155">문화권은 지역화 미들웨어에 의해 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-155">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="3345b-156">`_Host` 페이지(`_Host.cshtml`)의 Razor 식은 응답의 일부로 cookie에 문화권을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-156">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="3345b-157">브라우저는 WebSocket 연결을 열어 대화형 Blazor Server 세션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-157">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="3345b-158">지역화 미들웨어는 cookie를 읽고 문화권을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-158">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="3345b-159">Blazor Server 세션이 올바른 문화권으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-159">The Blazor Server session begins with the correct culture.</span></span>

<span data-ttu-id="3345b-160"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage>로 작업하는 경우 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-160">When working with a <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage>, use the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context> property:</span></span>

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

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="3345b-161">문화권을 선택하기 위한 UI 제공</span><span class="sxs-lookup"><span data-stu-id="3345b-161">Provide UI to choose the culture</span></span>

<span data-ttu-id="3345b-162">사용자가 문화권을 선택하기 위한 UI를 제공하려면 *리디렉션 기반 접근 방식* 이 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-162">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="3345b-163">이 프로세스는 사용자가 보안 리소스에 액세스하려고 할 때 웹앱에서 발생하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-163">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="3345b-164">즉, 사용자는 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-164">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="3345b-165">앱은 컨트롤러에 대한 리디렉션을 통해 사용자가 선택한 문화권을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-165">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="3345b-166">컨트롤러는 사용자가 선택한 문화권을 cookie로 설정하고 사용자를 원래 URI로 다시 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-166">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="3345b-167">서버에서 HTTP 엔드포인트를 설정하여 사용자가 선택한 문화권을 cookie에 설정하고 다시 원래 URI로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-167">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="3345b-168"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> 작업 결과를 사용하여 오픈 리디렉션 공격을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-168">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="3345b-169">자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3345b-169">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="3345b-170">앱이 컨트롤러 작업을 처리하도록 구성되지 않은 경우 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-170">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="3345b-171">`Startup.ConfigureServices`의 서비스 컬렉션에 MVC 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-171">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="3345b-172">`Startup.Configure`에서 컨트롤러 엔드포인트 라우팅을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-172">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="3345b-173">다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행하는 방법의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="3345b-173">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3345b-174">추가 자료</span><span class="sxs-lookup"><span data-stu-id="3345b-174">Additional resources</span></span>

* <xref:fundamentals/localization>
