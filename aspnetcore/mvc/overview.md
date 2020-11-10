---
title: ASP.NET Core MVC 개요
author: ardalis
description: 모델-보기-컨트롤러 디자인 패턴을 사용하여 웹앱 및 API를 빌드할 수 있는 풍부한 프레임워크인 ASP.NET Core MVC에 대해 알아봅니다.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/overview
ms.openlocfilehash: 4f0e98057c03769a672ef350cc3ec724a4f7e721
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431063"
---
# <a name="overview-of-aspnet-core-mvc"></a>ASP.NET Core MVC 개요

작성자: [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC는 모델-보기-컨트롤러 디자인 패턴을 사용하여 웹앱 및 API를 만들 수 있는 풍부한 프레임워크입니다.

## <a name="what-is-the-mvc-pattern"></a>MVC 패턴이란?

MVC(모델-뷰-컨트롤러) 아키텍처 패턴은 애플리케이션을 모델, 보기, 컨트롤러라는 세 가지 주요 구성 요소로 구분합니다. 이 패턴은 [문제를 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)하는 데 도움이 됩니다. 이 패턴을 사용하면 사용자 요청은 모델 작업을 담당하는 컨트롤러에 라우팅되어 사용자 작업을 수행하고/수행하거나 쿼리 결과를 검색합니다. 컨트롤러는 사용자에게 표시할 보기를 선택하고, 보기에 필요한 모델 데이터를 제공합니다.

다음 다이어그램은 세 가지 주요 구성 요소 및 다른 구성 요소를 참조하는 구성 요소를 보여줍니다.

![MVC 패턴](overview/_static/mvc.png)

이와 같은 명확한 책임의 규정은 복잡성의 측면에서 애플리케이션의 크기를 조정하는 데 도움을 주는데, 작업이 하나만 있는 항목(모델, 보기 또는 컨트롤러)을 좀 더 쉽게 코딩, 디버그 및 테스트할 수 있기 때문입니다. 이러한 세 영역 중 두 개 이상에 종속된 코드는 업데이트, 테스트 및 디버깅이 더 어렵습니다. 예를 들어 사용자 인터페이스 논리는 비즈니스 논리보다 자주 변하는 경향이 있습니다. 프레젠테이션 코드와 비즈니스 논리가 단일 개체에 결합되면 사용자 인터페이스가 변경될 때마다 비즈니스 논리를 포함하고 있는 개체를 수정해야 합니다. 이 때문에 자주 오류가 발생하며 모든 사소한 사용자 인터페이스 변경 시마다 비즈니스 논리를 다시 테스트해야 합니다.

> [!NOTE]
> 보기와 컨트롤러는 모두 모델에 의존합니다. 그러나 모델은 보기 및 컨트롤러에 의존하지 않습니다. 이것이 바로 분리의 주요 이점 중 하나입니다. 이와 같은 분리 덕분에 시각적 표시에 관계없이 모델을 만들고 테스트할 수 있습니다.

### <a name="model-responsibilities"></a>모델의 책임

MVC 애플리케이션의 모델은 애플리케이션 및 비즈니스 논리 또는 이를 통해 수행해야 하는 작업의 상태를 나타냅니다. 비즈니스 논리는 애플리케이션의 상태를 유지하기 위한 구현 논리와 함께 모델에 캡슐화해야 합니다. 강력한 형식의 보기는 일반적으로 해당 보기에 표시할 데이터를 포함하도록 디자인된 ViewModel 형식을 사용합니다. 컨트롤러는 모델에서 이러한 ViewModel 인스턴스를 만들고 채웁니다.

### <a name="view-responsibilities"></a>보기의 책임

보기는 사용자 인터페이스를 통해 콘텐츠를 제공할 책임이 있습니다. [ Razor 뷰 엔진](#razor-view-engine) 을 사용 하 여 .NET 코드를 HTML 태그에 포함 합니다. 보기 내부의 논리를 최소화해야 하며, 보기의 모든 논리는 콘텐츠 제공과 관련되어 있어야 합니다. 복잡한 모델의 데이터를 표시하기 위해 보기에서 다량의 논리를 수행해야 하는 경우 [보기 구성 요소](views/view-components.md), ViewModel 또는 보기 템플릿을 사용하여 보기를 간소화하는 방안을 고려해 보세요.

### <a name="controller-responsibilities"></a>컨트롤러의 책임

컨트롤러는 사용자 상호 작용을 처리하고, 모델을 작업하고, 궁극적으로 렌더링할 보기를 선택하는 구성 요소입니다. MVC 애플리케이션에서 보기는 정보만 표시합니다. 컨트롤러가 사용자 입력 및 상호 작용을 처리하고 응답합니다. MVC 패턴에서 컨트롤러는 초기 진입점으로, 작업할 모델과 렌더링할 보기를 선택할 책임이 있습니다(그 이름처럼 앱이 지정된 요청에 응답하는 방식을 제어).

> [!NOTE]
> 컨트롤러에 너무 많은 책임을 부여하여 지나치게 복잡하게 만들면 안 됩니다. 컨트롤러 논리가 너무 복잡해지지 않도록 컨트롤러에서 도메인 모델로 비즈니스 논리를 푸시합니다.

>[!TIP]
> 컨트롤러 작업이 같은 종류의 작업을 자주 수행하는 것을 발견하는 경우 이러한 공통 작업을 [필터](#filters)로 이동합니다.

## <a name="what-is-aspnet-core-mvc"></a>ASP.NET Core MVC란?

ASP.NET Core MVC 프레임워크는 ASP.NET Core에 사용하도록 최적화된 가볍고 테스트가 용이한 프레젠테이션 프레임워크입니다.

ASP.NET Core MVC는 문제를 깔끔하게 분리하는 동적 웹 사이트를 빌드하는 강력한 패턴 기반 방식입니다. 태그를 완벽하게 제어할 수 있고, TDD에 친숙한 개발을 지원하고, 최신 웹 표준을 사용합니다.

## <a name="features"></a>기능

ASP.NET Core MVC는 다음과 같은 기능을 포함하고 있습니다.

* [라우팅](#routing)
* [모델 바인딩](#model-binding)
* [모델 유효성 검사](#model-validation)
* [종속성 주입](../fundamentals/dependency-injection.md)
* [필터](#filters)
* [Areas](#areas)
* [Web API](#web-apis)
* [테스트 가능성](#testability)
* [Razor 뷰 엔진](#razor-view-engine)
* [강력한 형식의 뷰](#strongly-typed-views)
* [태그 도우미](#tag-helpers)
* [구성 요소 보기](#view-components)

### <a name="routing"></a>라우팅

ASP.NET Core MVC는 [ASP.NET Core 라우팅](../fundamentals/routing.md)을 기반으로 하며, 알기 쉽고 검색 가능한 URL이 있는 애플리케이션을 빌드할 수 있는 강력한 URL 매핑 구성 요소입니다. 웹 서버에 있는 파일의 구성 방식에 관계없이 SEO(검색 엔진 최적화) 및 링크 생성에 적합한 애플리케이션 URL 이름 지정 패턴을 정의할 수 있습니다. 경로 값 제약 조건, 기본값 및 선택적 값을 지원하는 편리한 경로 템플릿 구문을 사용하여 경로를 정의할 수 있습니다.

*규칙 기반 라우팅* 을 사용하면 응용 프로그램이 허용하는 URL 형식과 이러한 각 형식이 특정 컨트롤러의 특정 작업 메서드에 매핑되는 방식을 전체적으로 정의할 수 있습니다. 들어오는 요청이 수신되면 라우팅 엔진이 URL을 구문 분석하여 정의된 URL 형식 중 하나와 매칭한 후 관련 컨트롤러의 작업 메서드를 호출합니다.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*특성 라우팅* 을 사용하면 응용 프로그램의 경로를 정의하는 특성으로 컨트롤러와 작업을 데코레이팅하여 라우팅 정보를 지정할 수 있습니다. 즉, 경로 정의는 연결된 컨트롤러 및 작업 옆에 배치됩니다.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a>모델 바인딩

ASP.NET Core MVC [모델 바인딩](models/model-binding.md)은 클라이언트 요청 데이터(양식 값, 경로 데이터, 쿼리 문자열 매개 변수, HTTP 헤더)를 컨트롤러에서 처리할 수 있는 개체로 변환합니다. 그 결과, 컨트롤러 논리는 들어오는 요청 데이터를 파악하는 작업을 할 필요가 없습니다. 데이터를 작업 메서드에 대한 매개 변수로 갖고 있기 때문입니다.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a>모델 유효성 검사

ASP.NET Core MVC는 모델 개체를 데이터 주석 유효성 검사 특성으로 데코레이팅하여 [유효성 검사](models/validation.md)를 지원합니다. 서버에 값이 게시되기 전에 클라이언트 쪽에서 유효성 검사 특성이 확인될 뿐만 아니라 서버에서도 컨트롤러 작업이 호출되기 전에 확인됩니다.

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

컨트롤러 작업:

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

프레임워크는 클라이언트 및 서버의 요청 데이터 유효성 검사를 처리합니다. 모델 형식에 지정된 유효성 검사 논리는 렌더링된 보기에 비간섭 주석으로 추가되고 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 통해 브라우저에 적용됩니다.

### <a name="dependency-injection"></a>종속성 주입

ASP.NET Core는 기본적으로 [DI(종속성 주입 )](../fundamentals/dependency-injection.md)를 지원합니다. ASP.NET Core MVC에서 [컨트롤러](controllers/dependency-injection.md)는 생성자를 통해서 필요한 서비스를 요청하여 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따르도록 할 수 있습니다.

또한 앱에서 `@inject` 지시문을 사용하여 [보기 파일에서 종속성 주입](views/dependency-injection.md)을 사용할 수 있습니다.

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>필터

[필터](controllers/filters.md)는 개발자가 예외 처리 또는 권한 부여 같은 교차 편집 문제를 캡슐화하도록 도와줍니다. 필터는 작업 메서드에 대한 사용자 지정 전처리 및 후처리 논리 실행을 지원하며, 지정된 요청에 대한 실행 파이프라인 내부의 특정 지점에서 실행되도록 구성할 수 있습니다. 필터는 컨트롤러 또는 작업에 특성으로 적용할 수 있습니다(또는 전역적으로 실행할 수도 있습니다). 여러 필터(예: `Authorize`)가 프레임워크에 포함되어 있습니다. `[Authorize]`는 MVC 권한 부여 필터를 만드는 데 사용되는 특성입니다.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>영역

[영역](controllers/areas.md)은 대규모 ASP.NET Core MVC 웹앱을 더 작은 기능 그룹으로 분할하는 방법을 제공합니다. 영역은 애플리케이션 내부의 MVC 구조입니다. MVC 프로젝트에서 모델, 컨트롤러, 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 보관되며 MVC는 명명 규칙을 사용하여 이러한 구성 요소 간의 관계를 만듭니다. 대형 앱의 경우 앱을 별도의 고급 기능 영역으로 나누는 것이 좋습니다. 예를 들어 체크 아웃, 대금 청구, 검색 등의 여러 사업부를 포함 하는 전자 상거래 앱입니다. 이러한 각 단위에는 고유한 논리적 구성 요소 뷰, 컨트롤러 및 모델이 있습니다.

### <a name="web-apis"></a>Web API

ASP.NET Core MVC는 웹 사이트를 구축할 수 있는 훌륭한 플랫폼일 뿐 아니라 웹 API 빌드를 잘 지원합니다. 브라우저 및 모바일 디바이스를 비롯한 광범위한 클라이언트에 연결하는 서비스를 빌드할 수 있습니다.

이 프레임워크는 JSON 또는 XML로 [데이터 형식을 지정](xref:web-api/advanced/formatting)하는 기본 제공 지원을 통해 HTTP 콘텐츠 협상을 지원합니다. [사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 작성하여 사용자 고유의 형식에 대한 지원을 추가할 수 있습니다.

링크 생성을 사용하여 하이퍼미디어를 지원할 수 있습니다. 여러 웹 응용 프로그램에서 Web API를 공유할 수 있도록 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 간단하게 지원할 수 있습니다.

### <a name="testability"></a>테스트 가능성

이 프레임워크의 인터페이스 및 종속성 주입 사용은 단위 테스트에 적합하며, [통합 테스트](xref:test/integration-tests)를 쉽고 빠르게 수행할 수 있는 기능(예: Entity Framework용 TestHost 및 InMemory 공급자)을 포함하고 있습니다. [컨트롤러 논리를 테스트하는 방법](controllers/testing.md)에 대해 자세히 알아보세요.

### <a name="no-locrazor-view-engine"></a>Razor 뷰 엔진

[MVC 뷰 ASP.NET Core](views/overview.md) 뷰 [ Razor 엔진](views/razor.md) 을 사용 하 여 뷰를 렌더링 합니다. Razor 는 포함 된 c # 코드를 사용 하 여 뷰를 정의 하기 위한 간결 하 고, 표현 하기 위한 유체 템플릿 태그 언어입니다. Razor 는 서버에서 웹 콘텐츠를 동적으로 생성 하는 데 사용 됩니다. 서버 코드를 클라이언트 쪽 콘텐츠 및 코드와 깔끔하게 혼합할 수 있습니다.

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

Razor뷰 엔진을 사용 하 여 [레이아웃](views/layout.md), [부분 뷰](views/partial.md) 및 대체 가능 섹션을 정의할 수 있습니다.

### <a name="strongly-typed-views"></a>강력한 형식의 보기

Razor MVC의 뷰는 모델을 기반으로 강력 하 게 형식화 될 수 있습니다. 컨트롤러는 강력한 형식의 모델을 보기에 전달하여 보기에서 형식을 검사하고 IntelliSense를 지원할 수 있습니다.

예를 들어 다음 보기는 `IEnumerable<Product>` 형식의 모델을 렌더링합니다.

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>태그 도우미

[태그 도우미](views/tag-helpers/intro.md) 를 사용 하면 서버 쪽 코드에서 파일에 HTML 요소를 만들고 렌더링 하는 데 참여할 수 Razor 있습니다. 태그 도우미를 사용하여 사용자 지정 태그를 정의하거나(예: `<environment>`) 기존 태그의 동작을 수정할 수 있습니다(예: `<label>`). 태그 도우미는 요소 이름 및 해당 특성에 따라 특정 요소에 바인딩합니다. 서버 쪽 렌더링의 이점을 제공하면서도 HTML 편집 환경을 유지합니다.

양식 작성, 링크, 자산 로드 등의 일반적인 작업을 위한 여러 가지 기본 제공 태그 도우미가 있으며, 공용 GitHub 리포지토리 및 NuGet 패키지로도 사용할 수 있습니다. 태그 도우미는 C#에서 작성되며 요소 이름, 특성 이름 또는 부모 태그 기반의 HTML 요소를 대상으로 합니다. 예를 들어 기본 제공 LinkTagHelper를 사용하여 `AccountsController`의 `Login` 작업에 대한 링크를 만들 수 있습니다.

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

`EnvironmentTagHelper`를 사용하여 개발, 스테이징 또는 프로덕션 같은 런타임 환경에 따라 다른 스크립트(예: 원본 또는 최소화된)를 보기에 포함할 수 있습니다.

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

태그 도우미는 html 기반의 개발 환경과 HTML 및 태그를 만들기 위한 풍부한 IntelliSense 환경을 제공 Razor 합니다. 대부분의 기본 제공 태그 도우미는 기존 HTML 요소를 대상으로 하며 요소에 대한 서버 쪽 특성을 제공합니다.

### <a name="view-components"></a>보기 구성 요소

[보기 구성 요소](views/view-components.md)를 통해 렌더링 논리를 패키지하여 응용 프로그램에서 다시 사용할 수 있습니다. 보기 구성 요소는 [부분 보기](views/partial.md)와 비슷하지만, 논리가 연결되어 있습니다.

## <a name="compatibility-version"></a>호환성 버전

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드를 사용하면 ASP.NET Core MVC 2.1 이상에서 도입된 주요 동작 변경 내용을 앱이 옵트인(opt-in) 또는 옵트아웃(opt-out)할 수 있습니다.

자세한 내용은 <xref:mvc/compatibility-version>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* [AspNetCore-ASP.NET CORE mvc에 대 한 흐름 테스트 라이브러리](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): 강력한 형식의 단위 테스트 라이브러리로, mvc 및 web API 앱 테스트를 위한 흐름 인터페이스를 제공 합니다. ( *Microsoft에서 유지 관리하거나 지원하지 않습니다.* )
* <xref:blazor/components/prerendering-and-integration>
