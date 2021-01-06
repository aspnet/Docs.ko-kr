---
title: ASP.NET Core에서 Razor Pages 경로 및 앱 규칙
author: rick-anderson
description: 경로 및 앱 모델 공급자 규칙을 통해 페이지 라우팅, 검색 및 처리를 제어하는 방법을 검색합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 2947bf0b697ca01f17d260b9f31aa3cc79d457b6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059873"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core에서 Razor Pages 경로 및 앱 규칙

::: moniker range=">= aspnetcore-3.0"

페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor Pages 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.

개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.

페이지 경로를 지정하거나, 경로 세그먼트를 추가하거나, 경로에 매개 변수를 추가하려면 페이지의 `@page` 지시문을 사용합니다. 자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조하세요.

경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다. 자세한 내용은 [라우팅: 예약된 라우팅 이름](xref:mvc/controllers/routing#reserved-routing-names)을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

| 시나리오 | 샘플에서는 다음 사항을 보여줍니다. |
| -------- | --------------------------- |
| [모델 규칙](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 경로 템플릿 및 헤더를 앱의 페이지에 추가합니다. |
| [페이지 경로 작업 규칙](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다. |
| [페이지 모델 작업 규칙](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</li></ul> | 폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다. |

Razor Pages 규칙은 `Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>를 구성하는 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 오버로드를 사용하여 구성됩니다. 다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a>경로 순서

경로는 처리(경로 일치)를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>를 지정합니다.

| 순서            | 동작 |
| :--------------: | -------- |
| -1               | 경로는 다른 경로가 처리되기 전에 처리됩니다. |
| 0                | 순서가 지정되지 않았습니다(기본값). `Order`를 할당하지 않으면(`Order = null`) 기본적으로 경로 `Order`는 처리를 위해 0이 됩니다. |
| 1, 2, &hellip; n | 경로 처리 순서를 지정합니다. |

경로 처리는 다음 규칙에 따라 설정됩니다.

* 경로는 순서대로 처리됩니다(-1, 0, 1, 2, &hellip; n).
* 경로에 동일한 `Order`가 있는 경우 가장 구체적인 경로가 먼저 일치되고, 그 다음에 덜 구체적인 경로가 일치됩니다.
* 동일한 `Order`와 동일한 수의 매개 변수를 지정한 경로가 요청 URL과 일치하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가된 순서대로 처리됩니다.

가능하면 설정된 경로 처리 순서를 따르지 않도록 합니다. 일반적으로 라우팅은 URL이 일치하는 올바른 경로를 선택합니다. 경로 `Order` 속성을 경로 요청으로 올바르게 설정해야 하는 경우에는 앱의 라우팅 체계가 클라이언트에 혼동을 가져올 수 있으며 유지 관리하기 어려울 수 있습니다. 앱의 라우팅 체계를 간소화하는 방안을 모색하세요. 샘플 앱은 단일 앱을 사용하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요합니다. 그러나 프로덕션 앱에서는 경로 `Order` 설정을 연습하지 않는 것이 좋습니다.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. MVC의 경로 순서 관련 항목은 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)에서 확인할 수 있습니다.

## <a name="model-conventions"></a>모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>의 대리자를 추가하여 Razor Pages에 적용되는 [모델 규칙](xref:mvc/controllers/application-model#conventions)을 추가합니다.

### <a name="add-a-route-model-convention-to-all-pages"></a>모든 페이지에 경로 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고, 페이지 경로 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다. 이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작이 수행됩니다.

* `TheContactPage/{text?}`에 대한 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. 연락처 페이지 경로는 기본 순서가 `null`(`Order = 0`)이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치됩니다.
* `{aboutTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. `{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. `/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.
* `{otherPagesTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. `{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. *Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 추가와 같은 Razor Pages 옵션은 Razor Pages가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가될 때 추가됩니다. 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>모든 페이지에 앱 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고, 페이지 앱 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다. 클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다. 이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다. 전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.

샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>모든 페이지에 처리기 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>을 만들고, 페이지 처리기 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>페이지 경로 작업 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생되는 기본 경로 모델 공급자는 페이지 경로를 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.

### <a name="folder-route-model-convention"></a>폴더 경로 모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>을 사용하여 지정된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.

샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다. *Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수 값(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>페이지 경로 모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.

샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다. `/About/RouteDataValue`에서 경로 매개 변수 값을 사용하여 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>매개 변수 변환기를 사용하여 페이지 경로 사용자 지정

ASP.NET Core에서 생성된 페이지 경로는 매개 변수 변환기를 사용하여 사용자 지정할 수 있습니다. 매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다. 예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.

`PageRouteTransformerConvention` 페이지 경로 모델 규칙은 앱에서 자동으로 생성된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용합니다. 예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml* 의 Razor Pages 파일은 경로가 `/SubscriptionManagement/ViewAll`에서 `/subscription-management/view-all`로 다시 작성됩니다.

`PageRouteTransformerConvention`은 Razor Pages 폴더 및 파일 이름에서 가져온 자동으로 생성된 페이지 경로 세그먼트만 변환합니다. `@page` 지시문을 사용하여 추가된 경로 세그먼트는 변환하지 않습니다. 또한 이 규칙은 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>에 의해 추가된 경로를 변환하지 않습니다.

`PageRouteTransformerConvention`은 `Startup.ConfigureServices`에서 옵션으로 등록됩니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>페이지 경로 구성

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>를 사용하여 지정된 페이지 경로에 페이지에 대한 경로를 구성합니다. 페이지에 생성된 링크는 지정된 경로를 사용합니다. `AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.

샘플 앱은 *Contact.cshtml* 의 `/TheContactPage`에 대한 경로를 만듭니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.

연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다. 방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요. 추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다. 렌더링된 페이지에서는 'text' 세그먼트 값을 보여줍니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>페이지 모델 작업 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>에서 파생되는 기본 페이지 모델 공급자는 페이지 모델을 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다. 이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.

샘플 앱은 이 섹션의 예제에서 응답 헤더를 적용하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>라는 `AddHeaderAttribute` 클래스를 사용합니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.

**폴더 앱 모델 규칙**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>을 사용하여 지정된 폴더에 있는 모든 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.

이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

**페이지 앱 모델 규칙**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.

샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

**필터 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 지정된 필터를 적용하도록 구성합니다. 필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다. 조건이 통과하는 경우 헤더가 추가됩니다. 그렇지 않으면 `EmptyFilter`이 적용됩니다.

`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다. Razor Pages에서 작업 필터를 무시하므로 경로에 `OtherPages/Page2`가 포함되지 않는 경우 의도한 대로 `EmptyFilter`가 작동하지 않습니다.

`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

**필터 팩터리 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 모든 Razor Pages에 [필터](xref:mvc/controllers/filters)를 적용하도록 지정된 센터를 구성합니다.

샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 필터 및 페이지 필터(IPageFilter)

Razor Pages가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor Pages에서 무시됩니다. 사용할 수 있는 다른 유형의 MVC 필터에는 [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)가 있습니다. 자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.

페이지 필터(<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용되는 필터입니다. 자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor Pages 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.

개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.

페이지 경로를 지정하거나, 경로 세그먼트를 추가하거나, 경로에 매개 변수를 추가하려면 페이지의 `@page` 지시문을 사용합니다. 자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조하세요.

경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다. 자세한 내용은 [라우팅: 예약된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

| 시나리오 | 샘플에서는 다음 사항을 보여줍니다. |
| -------- | --------------------------- |
| [모델 규칙](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 경로 템플릿 및 헤더를 앱의 페이지에 추가합니다. |
| [페이지 경로 작업 규칙](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다. |
| [페이지 모델 작업 규칙](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</li></ul> | 폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다. |

Razor Pages 규칙은 `Startup` 클래스의 서비스 컬렉션에서 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>에 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 사용하여 추가되고 구성됩니다. 다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>경로 순서

경로는 처리(경로 일치)를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>를 지정합니다.

| 순서            | 동작 |
| :--------------: | -------- |
| -1               | 경로는 다른 경로가 처리되기 전에 처리됩니다. |
| 0                | 순서가 지정되지 않았습니다(기본값). `Order`를 할당하지 않으면(`Order = null`) 기본적으로 경로 `Order`는 처리를 위해 0이 됩니다. |
| 1, 2, &hellip; n | 경로 처리 순서를 지정합니다. |

경로 처리는 다음 규칙에 따라 설정됩니다.

* 경로는 순서대로 처리됩니다(-1, 0, 1, 2, &hellip; n).
* 경로에 동일한 `Order`가 있는 경우 가장 구체적인 경로가 먼저 일치되고, 그 다음에 덜 구체적인 경로가 일치됩니다.
* 동일한 `Order`와 동일한 수의 매개 변수를 지정한 경로가 요청 URL과 일치하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가된 순서대로 처리됩니다.

가능하면 설정된 경로 처리 순서를 따르지 않도록 합니다. 일반적으로 라우팅은 URL이 일치하는 올바른 경로를 선택합니다. 경로 `Order` 속성을 경로 요청으로 올바르게 설정해야 하는 경우에는 앱의 라우팅 체계가 클라이언트에 혼동을 가져올 수 있으며 유지 관리하기 어려울 수 있습니다. 앱의 라우팅 체계를 간소화하는 방안을 모색하세요. 샘플 앱은 단일 앱을 사용하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요합니다. 그러나 프로덕션 앱에서는 경로 `Order` 설정을 연습하지 않는 것이 좋습니다.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. MVC의 경로 순서 관련 항목은 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)에서 확인할 수 있습니다.

## <a name="model-conventions"></a>모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>의 대리자를 추가하여 Razor Pages에 적용되는 [모델 규칙](xref:mvc/controllers/application-model#conventions)을 추가합니다.

### <a name="add-a-route-model-convention-to-all-pages"></a>모든 페이지에 경로 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고, 페이지 경로 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다. 이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작이 수행됩니다.

* `TheContactPage/{text?}`에 대한 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. 연락처 페이지 경로는 기본 순서가 `null`(`Order = 0`)이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치됩니다.
* `{aboutTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. `{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. `/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.
* `{otherPagesTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. `{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. *Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 추가와 같은 Razor Pages 옵션은 MVC가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가될 때 추가됩니다. 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>모든 페이지에 앱 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고, 페이지 앱 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다. 클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다. 이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다. 전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.

샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>모든 페이지에 처리기 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>을 만들고, 페이지 처리기 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>페이지 경로 작업 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생되는 기본 경로 모델 공급자는 페이지 경로를 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.

### <a name="folder-route-model-convention"></a>폴더 경로 모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>을 사용하여 지정된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.

샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다. *Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수 값(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>페이지 경로 모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.

샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다. `/About/RouteDataValue`에서 경로 매개 변수 값을 사용하여 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>매개 변수 변환기를 사용하여 페이지 경로 사용자 지정

ASP.NET Core에서 생성된 페이지 경로는 매개 변수 변환기를 사용하여 사용자 지정할 수 있습니다. 매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다. 예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.

`PageRouteTransformerConvention` 페이지 경로 모델 규칙은 앱에서 자동으로 생성된 페이지 경로의 폴더 및 파일 이름 세그먼트에 매개 변수 변환기를 적용합니다. 예를 들어 */Pages/SubscriptionManagement/ViewAll.cshtml* 의 Razor Pages 파일은 경로가 `/SubscriptionManagement/ViewAll`에서 `/subscription-management/view-all`로 다시 작성됩니다.

`PageRouteTransformerConvention`은 Razor Pages 폴더 및 파일 이름에서 가져온 자동으로 생성된 페이지 경로 세그먼트만 변환합니다. `@page` 지시문을 사용하여 추가된 경로 세그먼트는 변환하지 않습니다. 또한 이 규칙은 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>에 의해 추가된 경로를 변환하지 않습니다.

`PageRouteTransformerConvention`은 `Startup.ConfigureServices`에서 옵션으로 등록됩니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>페이지 경로 구성

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>를 사용하여 지정된 페이지 경로에 페이지에 대한 경로를 구성합니다. 페이지에 생성된 링크는 지정된 경로를 사용합니다. `AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.

샘플 앱은 *Contact.cshtml* 의 `/TheContactPage`에 대한 경로를 만듭니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.

연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다. 방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요. 추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다. 렌더링된 페이지에서는 'text' 세그먼트 값을 보여줍니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>페이지 모델 작업 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>에서 파생되는 기본 페이지 모델 공급자는 페이지 모델을 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다. 이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.

샘플 앱은 이 섹션의 예제에서 응답 헤더를 적용하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>라는 `AddHeaderAttribute` 클래스를 사용합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.

**폴더 앱 모델 규칙**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>을 사용하여 지정된 폴더에 있는 모든 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.

이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

**페이지 앱 모델 규칙**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.

샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

**필터 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 지정된 필터를 적용하도록 구성합니다. 필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다. 조건이 통과하는 경우 헤더가 추가됩니다. 그렇지 않으면 `EmptyFilter`이 적용됩니다.

`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다. Razor Pages에서 작업 필터를 무시하므로 경로에 `OtherPages/Page2`가 포함되지 않는 경우 의도한 대로 `EmptyFilter`가 작동하지 않습니다.

`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

**필터 팩터리 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 모든 Razor Pages에 [필터](xref:mvc/controllers/filters)를 적용하도록 지정된 센터를 구성합니다.

샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 필터 및 페이지 필터(IPageFilter)

Razor Pages가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor Pages에서 무시됩니다. 사용할 수 있는 다른 유형의 MVC 필터에는 [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)가 있습니다. 자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.

페이지 필터(<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용되는 필터입니다. 자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

페이지 [경로 및 앱 모델 공급자 규칙](xref:mvc/controllers/application-model#conventions)을 사용하여 Razor Pages 앱에서 페이지 라우팅, 검색 및 처리를 제어하는 방법을 알아봅니다.

개별 페이지에 대한 사용자 지정 페이지 경로를 구성해야 하는 경우 이 항목의 뒷부분에서 설명할 [AddPageRoute 규칙](#configure-a-page-route)을 사용하여 페이지에 대한 라우팅을 구성합니다.

페이지 경로를 지정하거나, 경로 세그먼트를 추가하거나, 경로에 매개 변수를 추가하려면 페이지의 `@page` 지시문을 사용합니다. 자세한 내용은 [사용자 지정 경로](xref:razor-pages/index#custom-routes)를 참조하세요.

경로 세그먼트 또는 매개 변수 이름으로 사용할 수 없는 예약어가 있습니다. 자세한 내용은 [라우팅: 예약된 라우팅 이름](xref:fundamentals/routing#reserved-routing-names)을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

| 시나리오 | 샘플에서는 다음 사항을 보여줍니다. |
| -------- | --------------------------- |
| [모델 규칙](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 경로 템플릿 및 헤더를 앱의 페이지에 추가합니다. |
| [페이지 경로 작업 규칙](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 폴더에 있는 페이지 및 단일 페이지에 경로 템플릿을 추가합니다. |
| [페이지 모델 작업 규칙](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter(필터 클래스, 람다 식 또는 필터 팩터리)</li></ul> | 폴더의 페이지에 헤더를 추가하고, 단일 페이지에 헤더를 추가하고, [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 구성하여 헤더를 앱의 페이지에 추가합니다. |

Razor Pages 규칙은 `Startup` 클래스의 서비스 컬렉션에서 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>에 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 확장 메서드를 사용하여 추가되고 구성됩니다. 다음 규칙 예제는 이 토픽의 뒷부분에서 설명합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>경로 순서

경로는 처리(경로 일치)를 위한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>를 지정합니다.

| 순서            | 동작 |
| :--------------: | -------- |
| -1               | 경로는 다른 경로가 처리되기 전에 처리됩니다. |
| 0                | 순서가 지정되지 않았습니다(기본값). `Order`를 할당하지 않으면(`Order = null`) 기본적으로 경로 `Order`는 처리를 위해 0이 됩니다. |
| 1, 2, &hellip; n | 경로 처리 순서를 지정합니다. |

경로 처리는 다음 규칙에 따라 설정됩니다.

* 경로는 순서대로 처리됩니다(-1, 0, 1, 2, &hellip; n).
* 경로에 동일한 `Order`가 있는 경우 가장 구체적인 경로가 먼저 일치되고, 그 다음에 덜 구체적인 경로가 일치됩니다.
* 동일한 `Order`와 동일한 수의 매개 변수를 지정한 경로가 요청 URL과 일치하는 경우 경로는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>에 추가된 순서대로 처리됩니다.

가능하면 설정된 경로 처리 순서를 따르지 않도록 합니다. 일반적으로 라우팅은 URL이 일치하는 올바른 경로를 선택합니다. 경로 `Order` 속성을 경로 요청으로 올바르게 설정해야 하는 경우에는 앱의 라우팅 체계가 클라이언트에 혼동을 가져올 수 있으며 유지 관리하기 어려울 수 있습니다. 앱의 라우팅 체계를 간소화하는 방안을 모색하세요. 샘플 앱은 단일 앱을 사용하는 여러 라우팅 시나리오를 보여 주기 위해 명시적인 경로 처리가 필요합니다. 그러나 프로덕션 앱에서는 경로 `Order` 설정을 연습하지 않는 것이 좋습니다.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. MVC의 경로 순서 관련 항목은 [컨트롤러 작업에 라우팅: 특성 경로 순서 지정](xref:mvc/controllers/routing#ordering-attribute-routes)에서 확인할 수 있습니다.

## <a name="model-conventions"></a>모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>의 대리자를 추가하여 Razor Pages에 적용되는 [모델 규칙](xref:mvc/controllers/application-model#conventions)을 추가합니다.

### <a name="add-a-route-model-convention-to-all-pages"></a>모든 페이지에 경로 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고, 페이지 경로 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

샘플 앱은 앱의 모든 페이지에 `{globalTemplate?}` 경로 템플릿을 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `1`로 설정합니다. 이렇게 하면 샘플 앱에서 다음과 같은 경로 일치 동작이 수행됩니다.

* `TheContactPage/{text?}`에 대한 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. 연락처 페이지 경로는 기본 순서가 `null`(`Order = 0`)이므로 `{globalTemplate?}` 경로 템플릿 이전에 일치됩니다.
* `{aboutTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. `{aboutTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. `/About/RouteDataValue`에서 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.
* `{otherPagesTemplate?}` 경로 템플릿은 이 항목의 뒷부분에 추가되어 있습니다. `{otherPagesTemplate?}` 템플릿이 `2`의 `Order`로 제공됩니다. *Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 추가와 같은 Razor Pages 옵션은 MVC가 `Startup.ConfigureServices`의 서비스 컬렉션에 추가될 때 추가됩니다. 예제는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)을 참조하세요.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

`localhost:5000/About/GlobalRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![GlobalRouteValue의 경로 세그먼트를 사용하여 정보 페이지를 요청합니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>모든 페이지에 앱 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고, 페이지 앱 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

이 항목의 뒷부분에서 이 규칙 및 다른 규칙을 설명하기 위해 샘플 앱에는 `AddHeaderAttribute` 클래스가 포함됩니다. 클래스 생성자가 `name` 문자열 및 `values` 문자열 배열을 허용합니다. 이러한 값은 응답 헤더를 설정하는 `OnResultExecuting` 메서드에 사용됩니다. 전체 클래스는 항목의 뒷부분에 나오는 [페이지 모델 작업 규칙](#page-model-action-conventions) 섹션에서 보여줍니다.

샘플 앱에서는 앱의 모든 페이지에 `GlobalHeader` 헤더를 추가하는 `AddHeaderAttribute` 클래스를 사용합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 GlobalHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>모든 페이지에 처리기 모델 규칙 추가

<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>을 사용하여 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>을 만들고, 페이지 처리기 모델을 구축하는 동안 적용되는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 인스턴스의 컬렉션에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>페이지 경로 작업 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>에서 파생되는 기본 경로 모델 공급자는 페이지 경로를 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다.

### <a name="folder-route-model-convention"></a>폴더 경로 모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>을 사용하여 지정된 폴더의 모든 페이지에 대해 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.

샘플 앱에서는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>를 사용하여 `{otherPagesTemplate?}` 경로 템플릿을 *OtherPages* 폴더의 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다. *Pages/OtherPages* 폴더의 모든 페이지가 경로 매개 변수 값(예: `/OtherPages/Page1/RouteDataValue`)를 사용하여 요청되면`Order` 속성 설정 때문에 "RouteDataValue"가 `RouteData.Values["globalTemplate"]`(`Order = 1`)에 로드되고 `RouteData.Values["otherPagesTemplate"]`(`Order = 2`)에는 로드되지 않습니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

`localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue`에서 샘플의 Page1 페이지를 요청하고 결과를 검사합니다.

![OtherPages 폴더의 Page1은 GlobalRouteValue 및 OtherPagesRouteValue라는 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>페이지 경로 모델 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>을 만들고 추가합니다.

샘플 앱에서는 `AddPageRouteModelConvention`를 사용하여 `{aboutTemplate?}` 경로 템플릿을 정보 페이지에 추가합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel>에 대한 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 속성을 `2`로 설정합니다. 그러면 단일 경로 값을 제공하는 경우 `{globalTemplate?}`에 대한 템플릿(항목의 앞에서 `1`로 설정)이 첫 번째 경로 데이터 값 위치에 지정된 우선 순위가 됩니다. `/About/RouteDataValue`에서 경로 매개 변수 값을 사용하여 정보 페이지를 요청하는 경우 "RouteDataValue"는 `Order` 속성 설정으로 인해 `RouteData.Values["aboutTemplate"]`(`Order = 2`)이 아닌 `RouteData.Values["globalTemplate"]`(`Order = 1`)으로 로드됩니다.

가능하면 `Order`는 설정하지 않도록 합니다. 설정하면 `Order = 0`이 됩니다. 라우팅을 사용하여 올바른 경로를 선택합니다.

`localhost:5000/About/GlobalRouteValue/AboutRouteValue`에서 샘플의 정보 페이지를 요청하고 결과를 검사합니다.

![정보 페이지는 GlobalRouteValue 및 AboutRouteValue에 대한 경로 세그먼트를 사용하여 요청됩니다. 렌더링된 페이지는 경로 데이터 값이 페이지의 OnGet 메서드에서 캡처되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>페이지 경로 구성

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>를 사용하여 지정된 페이지 경로에 페이지에 대한 경로를 구성합니다. 페이지에 생성된 링크는 지정된 경로를 사용합니다. `AddPageRoute`는 `AddPageRouteModelConvention`을 사용하여 경로를 설정합니다.

샘플 앱은 *Contact.cshtml* 의 `/TheContactPage`에 대한 경로를 만듭니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

연락처 페이지도 기본 경로를 통해 `/Contact`에 도달할 수 있습니다.

연락처 페이지에 대한 샘플 앱의 사용자 지정 경로는 선택적 `text` 경로 세그먼트(`{text?}`)에 허용됩니다. 방문자가 해당 `/Contact` 경로에 있는 페이지에 액세스하는 경우 페이지에는 해당 `@page` 지시문에 있는 이 선택적 세그먼트가 포함됩니다.

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

렌더링된 페이지의 **연락처** 링크에 생성된 URL이 업데이트된 경로를 반영합니다.

![탐색 모음의 샘플 앱 연락처 링크](razor-pages-conventions/_static/contact-link.png)

![렌더링된 HTML에서 연락처 링크를 검사하면 href가 '/TheContactPage'로 설정되어 있음을 나타냅니다.](razor-pages-conventions/_static/contact-link-source.png)

일반적인 경로 `/Contact` 또는 사용자 지정 경로 `/TheContactPage`에서 연락처 페이지를 방문하세요. 추가 `text` 경로 세그먼트를 제공하는 경우 페이지는 제공한 HTML 인코딩 세그먼트를 보여줍니다.

![URL에서 'TextValue'라는 선택적 'text' 경로 세그먼트를 제공하는 에지 브라우저 예제입니다. 렌더링된 페이지에서는 'text' 세그먼트 값을 보여줍니다.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>페이지 모델 작업 규칙

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>에서 파생되는 기본 페이지 모델 공급자는 페이지 모델을 구성하기 위한 확장성 지점을 제공하도록 설계된 규칙을 호출합니다. 이러한 규칙은 페이지 검색 및 처리 시나리오를 빌드하고 수정하는 경우에 유용합니다.

샘플 앱은 이 섹션의 예제에서 응답 헤더를 적용하는 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>라는 `AddHeaderAttribute` 클래스를 사용합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

샘플을 규칙을 사용하여 폴더에 있는 모든 페이지 및 단일 페이지에 특성을 적용하는 방법을 보여줍니다.

**폴더 앱 모델 규칙**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>을 사용하여 지정된 폴더에 있는 모든 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 인스턴스에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.

이 샘플은 *OtherPages* 폴더 내의 페이지에 `OtherPagesHeader` 헤더를 추가하여 앱의 `AddFolderApplicationModelConvention`을 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

`localhost:5000/OtherPages/Page1`에서 샘플의 Page1 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPages/Page1 페이지의 응답 헤더는 OtherPagesHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/page1-otherpages-header.png)

**페이지 앱 모델 규칙**

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>을 사용하여 지정된 이름을 갖는 페이지의 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>에 대해 작업을 호출하는 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>을 만들고 추가합니다.

샘플은 정보 페이지에 `AboutHeader` 헤더를 추가하여 `AddPageApplicationModelConvention`를 사용하는 방법을 보여줍니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더는 AboutHeader가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-about-header.png)

**필터 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 지정된 필터를 적용하도록 구성합니다. 필터 클래스를 구현할 수 있지만 샘플 앱은 람다 식으로 필터를 구현하는 방법을 보여줍니다. 그러면 백그라운드에서 필터를 반환하는 팩터리로 구현됩니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

페이지 앱 모델은 *OtherPages* 폴더에 있는 Page2 페이지로 이동하는 세그먼트에 대한 상대 경로를 확인하는 데 사용됩니다. 조건이 통과하는 경우 헤더가 추가됩니다. 그렇지 않으면 `EmptyFilter`이 적용됩니다.

`EmptyFilter`은 [작업 필터](xref:mvc/controllers/filters#action-filters)입니다. Razor Pages에서 작업 필터를 무시하므로 경로에 `OtherPages/Page2`가 포함되지 않는 경우 의도한 대로 `EmptyFilter`가 작동하지 않습니다.

`localhost:5000/OtherPages/Page2`에서 샘플의 Page2 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![OtherPagesPage2Header는 Page2에 대한 응답에 추가됩니다.](razor-pages-conventions/_static/page2-filter-header.png)

**필터 팩터리 구성**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>는 모든 Razor Pages에 [필터](xref:mvc/controllers/filters)를 적용하도록 지정된 센터를 구성합니다.

샘플 앱은 앱의 페이지에 두 개의 값이 포함된 `FilterFactoryHeader` 헤더를 추가하여 [필터 팩터리](xref:mvc/controllers/filters#ifilterfactory)를 사용하는 예제를 제공합니다.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

`localhost:5000/About`에서 샘플의 정보 페이지를 요청하고 헤더를 검사하여 결과를 확인합니다.

![정보 페이지의 응답 헤더에서는 두 개의 FilterFactoryHeader 헤더가 추가되었음을 보여줍니다.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 필터 및 페이지 필터(IPageFilter)

Razor Pages가 처리기 메서드를 사용하므로 MVC [작업 필터](xref:mvc/controllers/filters#action-filters)는 Razor Pages에서 무시됩니다. 사용할 수 있는 다른 유형의 MVC 필터에는 [권한 부여](xref:mvc/controllers/filters#authorization-filters), [예외](xref:mvc/controllers/filters#exception-filters), [리소스](xref:mvc/controllers/filters#resource-filters) 및 [결과](xref:mvc/controllers/filters#result-filters)가 있습니다. 자세한 내용은 [필터](xref:mvc/controllers/filters) 항목을 참조하세요.

페이지 필터(<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>)는 Razor Pages에 적용되는 필터입니다. 자세한 내용은 [Razor Pages에 대한 필터 메서드](xref:razor-pages/filter)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
