---
title: 'ASP.NET Core :::no-loc(Blazor)::: 세계화 및 지역화'
author: guardrex
description: '여러 문화권과 언어의 사용자가 :::no-loc(Razor)::: 구성 요소에 액세스할 수 있도록 하는 방법을 알아봅니다.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: f8f261f25d854a9bf36ad3299f4af392d5c4fafd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055882"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="19d9b-103">ASP.NET Core :::no-loc(Blazor)::: 세계화 및 지역화</span><span class="sxs-lookup"><span data-stu-id="19d9b-103">ASP.NET Core :::no-loc(Blazor)::: globalization and localization</span></span>

<span data-ttu-id="19d9b-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="19d9b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="19d9b-105">여러 문화권과 언어의 사용자가 :::no-loc(Razor)::: 구성 요소에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-105">:::no-loc(Razor)::: components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="19d9b-106">다음 .NET 세계화 및 지역화 시나리오를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="19d9b-107">. NET의 리소스 시스템</span><span class="sxs-lookup"><span data-stu-id="19d9b-107">.NET's resources system</span></span>
* <span data-ttu-id="19d9b-108">문화권별 숫자 및 날짜 형식 지정</span><span class="sxs-lookup"><span data-stu-id="19d9b-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="19d9b-109">다음과 같은 제한된 ASP.NET Core의 지역화 시나리오가 현재 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="19d9b-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 및 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601>는 :::no-loc(Blazor)::: 앱에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="19d9b-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> 및 데이터 주석 지역화는 ASP.NET Core MVC 시나리오로, :::no-loc(Blazor)::: 앱에서 **지원되지 않습니다**.</span><span class="sxs-lookup"><span data-stu-id="19d9b-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="19d9b-112">자세한 내용은 <xref:fundamentals/localization>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="19d9b-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="19d9b-113">전역화</span><span class="sxs-lookup"><span data-stu-id="19d9b-113">Globalization</span></span>

<span data-ttu-id="19d9b-114">:::no-loc(Blazor):::의 [`@bind`](xref:mvc/views/razor#bind) 기능은 사용자의 현재 문화권에 따라 표시하기 위해 형식을 지정하고 값을 구문 분석합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-114">:::no-loc(Blazor):::'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="19d9b-115">현재 문화권은 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 속성에서 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="19d9b-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>는 다음 필드 형식(`<input type="{TYPE}" />`)에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="19d9b-117">이전 필드 형식:</span><span class="sxs-lookup"><span data-stu-id="19d9b-117">The preceding field types:</span></span>

* <span data-ttu-id="19d9b-118">적절한 브라우저 기반 서식 지정 규칙을 사용하여 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="19d9b-119">자유 형식 텍스트를 포함할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="19d9b-120">브라우저의 구현에 따라 사용자 상호 작용 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="19d9b-121">다음 필드 형식은 특정 형식 지정 요구 사항이 있으며, 모든 주요 브라우저에서 지원되는 것은 아니므로 현재 :::no-loc(Blazor):::에서 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-121">The following field types have specific formatting requirements and aren't currently supported by :::no-loc(Blazor)::: because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="19d9b-122">[`@bind`](xref:mvc/views/razor#bind)는 `@bind:culture` 매개 변수를 지원하여 값을 구문 분석하고 서식을 지정하기 위한 <xref:System.Globalization.CultureInfo?displayProperty=fullName>을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="19d9b-123">`date` 및 `number` 필드 형식을 사용할 때는 문화권을 지정하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="19d9b-124">`date` 및 `number`는 필수 문화권을 제공하는 :::no-loc(Blazor):::가 기본적으로 제공되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-124">`date` and `number` have built-in :::no-loc(Blazor)::: support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="19d9b-125">지역화</span><span class="sxs-lookup"><span data-stu-id="19d9b-125">Localization</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="19d9b-126">:::no-loc(Blazor WebAssembly)::: 앱은 사용자의 [언어 기본 설정](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)을 사용하여 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-126">:::no-loc(Blazor WebAssembly)::: apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="19d9b-127">문화권을 명시적으로 구성하려면 `Program.Main`에서 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType>와 <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType>를 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="19d9b-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="19d9b-128">기본적으로 :::no-loc(Blazor WebAssembly):::는 날짜 및 통화와 같은 값을 사용자의 문화권에 표시하는 데 필요한 최소한의 세계화 리소스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-128">By default, :::no-loc(Blazor WebAssembly)::: carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="19d9b-129">문화권 변경을 동적으로 지원해야 하는 애플리케이션은 프로젝트 파일에서 `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData`를 구성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-129">Applications that must support dynamically changing the culture should configure `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>true</:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="19d9b-130">:::no-loc(Blazor WebAssembly):::는 `:::no-loc(Blazor):::.start`에 전달된 옵션을 사용하여 특정 애플리케이션 문화권으로 실행하도록 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-130">:::no-loc(Blazor WebAssembly)::: can also be configured to launch using a specific application culture using options passed to `:::no-loc(Blazor):::.start`.</span></span> <span data-ttu-id="19d9b-131">예를 들어 아래 샘플은 `en-GB` 문화권으로 실행하도록 구성된 앱을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  :::no-loc(Blazor):::.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="19d9b-132">`applicationCulture`의 값은 [BCP-47 언어 태그 형식](https://tools.ietf.org/html/bcp47)을 준수해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="19d9b-133">앱에 지역화가 필요하지 않은 경우 `en-US` 문화권을 기반으로 하는 고정 문화권을 지원하도록 앱을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="19d9b-134">기본적으로 :::no-loc(Blazor WebAssembly)::: 앱에 대한 IL(중간 언어) 링커 구성에서는 명시적으로 요청된 로캘을 제외한 국제화 정보를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-134">By default, the Intermediate Language (IL) Linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="19d9b-135">자세한 내용은 <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="19d9b-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="19d9b-136">:::no-loc(Blazor):::가 선택하는 문화권은 대부분의 사용자에게 충분할 수 있지만 사용자가 원하는 로캘을 지정할 방법을 제공하는 방안도 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="19d9b-136">While the culture that :::no-loc(Blazor)::: selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="19d9b-137">문화권 선택기가 포함된 :::no-loc(Blazor WebAssembly)::: 샘플 앱을 살펴보려면 [`LocSample`](https://github.com/pranavkm/LocSample) 지역화 샘플 앱을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="19d9b-137">For a :::no-loc(Blazor WebAssembly)::: sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="19d9b-138">:::no-loc(Blazor Server)::: 앱은 [지역화 미들웨어](xref:fundamentals/localization#localization-middleware)를 사용하여 지역화됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-138">:::no-loc(Blazor Server)::: apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="19d9b-139">미들웨어는 앱에서 리소스를 요청하는 사용자에게 적절한 문화권을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="19d9b-140">문화권은 다음 방법 중 하나를 사용하여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="19d9b-141">[:::no-loc(Cookie):::](#:::no-loc(cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="19d9b-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span></span>
* [<span data-ttu-id="19d9b-142">문화권을 선택하기 위한 UI 제공</span><span class="sxs-lookup"><span data-stu-id="19d9b-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="19d9b-143">자세한 내용과 예제를 보려면 <xref:fundamentals/localization>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="19d9b-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="19d9b-144">:::no-loc(Cookie):::s</span><span class="sxs-lookup"><span data-stu-id="19d9b-144">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="19d9b-145">지역화 문화권 :::no-loc(cookie):::는 사용자의 문화권을 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-145">A localization culture :::no-loc(cookie)::: can persist the user's culture.</span></span> <span data-ttu-id="19d9b-146">지역화 미들웨어는 후속 요청에서 :::no-loc(cookie):::를 읽어 사용자의 문화권을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-146">The Localization Middleware reads the :::no-loc(cookie)::: on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="19d9b-147">:::no-loc(cookie):::를 사용하면 WebSocket 연결이 문화권을 올바르게 전파할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-147">Use of a :::no-loc(cookie)::: ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="19d9b-148">지역화 체계가 URL 경로 또는 쿼리 문자열을 기준으로 하는 경우 스키마는 WebSocket을 사용하지 못할 수 있으므로 문화권이 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="19d9b-149">따라서 지역화 문화권 :::no-loc(cookie):::를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-149">Therefore, use of a localization culture :::no-loc(cookie)::: is the recommended approach.</span></span>

<span data-ttu-id="19d9b-150">문화권이 지역화 :::no-loc(cookie):::에 유지되는 경우 임의 기술을 사용하여 문화권을 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-150">Any technique can be used to assign a culture if the culture is persisted in a localization :::no-loc(cookie):::.</span></span> <span data-ttu-id="19d9b-151">앱에 서버 쪽 ASP.NET Core에 대해 설정된 지역화 체계가 이미 있는 경우 앱의 기존 지역화 인프라를 계속 사용하고 앱의 체계 내에서 지역화 문화권 :::no-loc(cookie):::를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture :::no-loc(cookie)::: within the app's scheme.</span></span>

<span data-ttu-id="19d9b-152">다음 예제에서는 지역화 미들웨어에서 읽을 수 있는 :::no-loc(cookie):::의 현재 문화권을 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-152">The following example shows how to set the current culture in a :::no-loc(cookie)::: that can be read by the Localization Middleware.</span></span> <span data-ttu-id="19d9b-153">`Pages/_Host.cshtml` 파일에서 여는 `<body>` 태그 바로 안에 :::no-loc(Razor)::: 식을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-153">Create a :::no-loc(Razor)::: expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.:::no-loc(Cookie):::s.Append(
            :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
            :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="19d9b-154">지역화는 다음과 같은 이벤트 시퀀스에 따라 앱에서 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="19d9b-155">브라우저가 앱에 초기 HTTP 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="19d9b-156">문화권은 지역화 미들웨어에 의해 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="19d9b-157">`_Host` 페이지(`_Host.cshtml`)의 :::no-loc(Razor)::: 식은 응답의 일부로 :::no-loc(cookie):::에 문화권을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-157">The :::no-loc(Razor)::: expression in the `_Host` page (`_Host.cshtml`) persists the culture in a :::no-loc(cookie)::: as part of the response.</span></span>
1. <span data-ttu-id="19d9b-158">브라우저는 WebSocket 연결을 열어 대화형 :::no-loc(Blazor Server)::: 세션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-158">The browser opens a WebSocket connection to create an interactive :::no-loc(Blazor Server)::: session.</span></span>
1. <span data-ttu-id="19d9b-159">지역화 미들웨어는 :::no-loc(cookie):::를 읽고 문화권을 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-159">The Localization Middleware reads the :::no-loc(cookie)::: and assigns the culture.</span></span>
1. <span data-ttu-id="19d9b-160">:::no-loc(Blazor Server)::: 세션이 올바른 문화권으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-160">The :::no-loc(Blazor Server)::: session begins with the correct culture.</span></span>

<span data-ttu-id="19d9b-161"><xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page>로 작업하는 경우 <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-161">When working with a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page>, use the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> property:</span></span>

```razor
@{
    this.Context.Response.:::no-loc(Cookie):::s.Append(
        :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
        :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="19d9b-162">문화권을 선택하기 위한 UI 제공</span><span class="sxs-lookup"><span data-stu-id="19d9b-162">Provide UI to choose the culture</span></span>

<span data-ttu-id="19d9b-163">사용자가 문화권을 선택하기 위한 UI를 제공하려면 *리디렉션 기반 접근 방식* 이 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-163">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="19d9b-164">이 프로세스는 사용자가 보안 리소스에 액세스하려고 할 때 웹앱에서 발생하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-164">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="19d9b-165">즉, 사용자는 로그인 페이지로 리디렉션되고 다시 원래 리소스로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-165">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="19d9b-166">앱은 컨트롤러에 대한 리디렉션을 통해 사용자가 선택한 문화권을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-166">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="19d9b-167">컨트롤러는 사용자가 선택한 문화권을 :::no-loc(cookie):::로 설정하고 사용자를 원래 URI로 다시 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-167">The controller sets the user's selected culture into a :::no-loc(cookie)::: and redirects the user back to the original URI.</span></span>

<span data-ttu-id="19d9b-168">서버에서 HTTP 엔드포인트를 설정하여 사용자가 선택한 문화권을 :::no-loc(cookie):::에 설정하고 다시 원래 URI로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-168">Establish an HTTP endpoint on the server to set the user's selected culture in a :::no-loc(cookie)::: and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.:::no-loc(Cookie):::s.Append(
                :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
                :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="19d9b-169"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> 작업 결과를 사용하여 오픈 리디렉션 공격을 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-169">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="19d9b-170">자세한 내용은 <xref:security/preventing-open-redirects>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="19d9b-170">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="19d9b-171">앱이 컨트롤러 작업을 처리하도록 구성되지 않은 경우 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-171">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="19d9b-172">`Startup.ConfigureServices`의 서비스 컬렉션에 MVC 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-172">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="19d9b-173">`Startup.Configure`에서 컨트롤러 엔드포인트 라우팅을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-173">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.Map:::no-loc(Blazor):::Hub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="19d9b-174">다음 구성 요소는 사용자가 문화권을 선택할 때 초기 리디렉션을 수행하는 방법의 예를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="19d9b-174">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="19d9b-175">추가 자료</span><span class="sxs-lookup"><span data-stu-id="19d9b-175">Additional resources</span></span>

* <xref:fundamentals/localization>
