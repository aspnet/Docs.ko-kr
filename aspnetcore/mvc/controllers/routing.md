---
title: ASP.NET Core의 컨트롤러 작업에 라우팅
author: rick-anderson
description: ASP.NET Core MVC가 라우팅 미들웨어를 사용하여 들어오는 요청의 URL을 일치시키고 이를 작업에 매핑하는 방법을 알아봅니다.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: a163c87fdb9a02c1b074ab32c19c11932c66cfd4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854537"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>ASP.NET Core의 컨트롤러 작업에 라우팅

작성자: [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 컨트롤러는 라우팅 [미들웨어](xref:fundamentals/middleware/index) 를 사용 하 여 들어오는 요청의 url을 일치 시키고이를 [작업](#action)에 매핑합니다.  경로 템플릿:

* 는 시작 코드 또는 특성에서 정의 됩니다.
* URL 경로가 [작업과](#action)일치 하는 방식을 설명 합니다.
* 는 링크에 대 한 Url을 생성 하는 데 사용 됩니다. 생성 된 링크는 일반적으로 응답에서 반환 됩니다.

작업은 [일반적으로 라우트된 경로](#cr) 또는 [특성 라우팅](#ar)입니다. 컨트롤러 또는 [작업](#action) 에 경로를 배치 하면 특성이 라우팅됩니다. 자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.

이 문서:

* MVC와 라우팅 간의 상호 작용에 대해 설명 합니다.
  * 일반적인 MVC 앱이 라우팅 기능을 사용 하는 방법입니다.
  * 모두 포함:
    * 일반적으로 컨트롤러 및 뷰에 사용 되는 [라우팅](#cr) 입니다.
    * REST Api와 함께 사용 되는 *특성 라우팅* 입니다. 주로 REST Api에 대 한 라우팅에 관심이 있는 경우 [Rest api에 대 한 특성 라우팅](#ar) 섹션으로 이동 합니다.
  * 라우팅 고급 라우팅에 대 한 자세한 내용은 [라우팅](xref:fundamentals/routing) 을 참조 하세요.
* ASP.NET Core 3.0에 추가 된 기본 라우팅 시스템 (끝점 라우팅 이라고 함)을 참조 합니다. 호환성을 위해 이전 버전의 라우팅으로 컨트롤러를 사용할 수 있습니다. 지침은 [2.2-3.0 마이그레이션 가이드](xref:migration/22-to-30) 를 참조 하세요. 레거시 라우팅 시스템에 대 한 참조 자료는 [이 문서의 2.2 버전](xref:mvc/controllers/routing?view=aspnetcore-2.2) 을 참조 하세요.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>기본 경로 설정

`Startup.Configure` 일반적으로 [기본 라우팅을](#crd)사용할 때 다음과 비슷한 코드가 있습니다.

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

호출 내 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 에서 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 는 단일 경로를 만드는 데 사용 됩니다. 단일 경로는 경로 라는 이름으로 지정 됩니다 `default` . 컨트롤러 및 뷰를 사용 하는 대부분의 앱은 경로와 유사한 경로 템플릿을 사용 `default` 합니다. REST Api는 [특성 라우팅을](#ar)사용 해야 합니다.

경로 템플릿 `"{controller=Home}/{action=Index}/{id?}"` :

* URL 경로와 일치 합니다. `/Products/Details/5`
* `{ controller = Products, action = Details, id = 5 }`경로를 토큰화 하 여 경로 값을 추출 합니다. 경로 값을 추출 하면 앱에 라는 컨트롤러와 작업이 포함 된 경우 일치 하는 항목이 생성 됩니다 `ProductsController` `Details` .

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5` 모델은 `id = 5` 매개 변수를로 설정 하기 위해의 값을 바인딩합니다 `id` `5` . 자세한 내용은 [모델 바인딩](xref:mvc/models/model-binding) 을 참조 하세요.
* `{controller=Home}``Home`는를 기본값으로 정의 `controller` 합니다.
* `{action=Index}``Index`는를 기본값으로 정의 `action` 합니다.
*  `?`의 문자는 `{id?}` 를 `id` 선택적으로 정의 합니다.
  * 기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다. 경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.
* URL 경로와 일치 `/` 합니다.
* 경로 값을 생성 `{ controller = Home, action = Index }` 합니다.

및에 대 한 값은 `controller` `action` 기본값을 사용 합니다. `id` URL 경로에 해당 세그먼트가 없기 때문에에서 값을 생성 하지 않습니다. `/` 및 작업이 있는 경우에만 `HomeController` 일치 `Index` :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

위의 컨트롤러 정의와 경로 템플릿을 사용 하 여 `HomeController.Index` 다음 URL 경로에 대해 작업을 실행 합니다.

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL 경로는 `/` 경로 템플릿 기본 `Home` 컨트롤러 및 작업을 사용 합니다 `Index` . URL 경로는 `/Home` 경로 템플릿 기본 작업을 사용 합니다 `Index` .

편의 메서드인 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>를 사용하여:

```csharp
endpoints.MapDefaultControllerRoute();
```

대체

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> 라우팅은 및 미들웨어를 사용 하 여 구성 됩니다 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> . 컨트롤러를 사용 하려면 다음을 수행 합니다.
>
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A>안쪽 `UseEndpoints` 을 호출 하 여 라우트된 컨트롤러 [특성](#ar) 을 매핑합니다.
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>또는 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 를 호출 하 여 대상 [](#cr) 컨트롤러 및 [특성 라우트된](#ar) 컨트롤러를 모두 매핑합니다.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>규칙 기반 라우팅

기존 라우팅은 컨트롤러 및 뷰에서 사용 됩니다. `default` 경로인:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

는 *규칙 기반 라우팅* 의 예제입니다. 이는 URL 경로에 대 한 *규칙* 을 설정 하기 때문에 *기존 라우팅* 이라고 합니다.

* 첫 번째 경로 세그먼트는 `{controller=Home}` 컨트롤러 이름에 매핑됩니다.
* 두 번째 세그먼트 인은 `{action=Index}` [작업](#action) 이름에 매핑됩니다.
* 세 번째 세그먼트는 `{id?}` 선택 사항에 사용 됩니다 `id` . `?`의를 `{id?}` 사용 하면이 옵션을 선택할 수 있습니다. `id` 는 모델 엔터티에 매핑하는 데 사용 됩니다.

이 경로를 사용 하 여 `default` URL 경로를 사용 합니다.

* `/Products/List` 작업에 매핑됩니다 `ProductsController.List` .
* `/Blog/Article/17` 는에 매핑되고 `BlogController.Article` 일반적으로 `id` 매개 변수를 17에 바인딩합니다.

이 매핑:

* 는 컨트롤러 및 [작업](#action) 이름만을 기반으로 **합니다.**
* 는 네임 스페이스, 소스 파일 위치 또는 메서드 매개 변수를 기반으로 하지 않습니다.

기본 경로를 사용 하 여 기본 라우팅을 사용 하면 각 작업에 대 한 새 URL 패턴을 제공 하지 않고도 앱을 만들 수 있습니다. [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 스타일 작업을 포함 하는 앱의 경우, 컨트롤러 간의 url에 대해 일관성을 유지 합니다.

* 코드를 간소화 하는 데 도움이 됩니다.
* UI를 보다 예측 가능 하 게 만듭니다.

> [!WARNING]
> `id`위의 코드에서은 경로 템플릿에서 선택적으로 정의 됩니다. URL의 일부로 제공 되는 선택적 ID 없이 작업을 실행할 수 있습니다. 일반적으로 `id` URL에서이 생략 된 경우:
>
> * `id` 모델 바인딩에서로 설정 됩니다 `0` .
> * 데이터베이스 일치에서 엔터티를 찾을 수 없습니다 `id == 0` .
>
> [특성 라우팅은](#ar) 사용자가 아닌 일부 작업에 필요한 ID를 설정 하는 세분화 된 제어를 제공 합니다. 규칙에 따라 설명서에는 `id` 올바른 사용에 표시 될 수 있는 것과 같은 선택적 매개 변수가 포함 됩니다.

대부분의 앱은 URL을 읽을 수 있고 의미를 담고 있도록 기본적이고 서술적인 라우팅 체계를 선택해야 합니다. 기본 기존 경로인 `{controller=Home}/{action=Index}/{id?}`는:

* 기본적이고 서술적인 라우팅 체계를 지원합니다.
* UI 기반 앱에 대한 유용한 시작점입니다.
* 는 많은 웹 UI 앱에 필요한 유일한 경로 템플릿입니다. 규모가 큰 웹 UI 앱의 경우 [영역](#areas) 을 사용 하는 또 다른 경로를 사용 하는 경우가 많습니다.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 및 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :

* 호출 된 순서에 따라 해당 끝점에 **순서** 값을 자동으로 할당 합니다.

ASP.NET Core 3.0 이상의 엔드포인트 라우팅은 다음과 같습니다.

* 경로 개념이 없습니다.
* 는 확장성 실행을 위한 순서 보증을 제공 하지 않으며 모든 끝점이 한 번에 처리 됩니다.

[로깅](xref:fundamentals/logging/index)을 사용하도록 설정하여 <xref:Microsoft.AspNetCore.Routing.Route>와 같은 기본 제공 라우팅 구현이 요청과 일치하는 방법을 확인하세요.

[특성 라우팅은](#ar) 이 문서의 뒷부분에서 설명 합니다.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>다중 기본 경로

[](#cr) `UseEndpoints` 및에 대 한 호출을 추가 하 여 내에서 여러 개의 기존 경로를 추가할 수 있습니다 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> . 이렇게 하면 여러 가지 규칙을 정의 하거나 다음과 같이 특정 [작업](#action)에 전용으로 사용 되는 기본 경로를 추가할 수 있습니다.

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

`blog`위의 코드에서 경로는 **전용 기본 경로** 입니다. 다음 이유 때문에 전용 기본 경로 라고 합니다.

* [기존 라우팅을](#cr)사용 합니다.
* 특정 [작업](#action)전용입니다.

`controller`및 `action` 는 경로 템플릿에 매개 변수로 표시 되지 않기 때문에 `"blog/{*article}"` 다음과 같습니다.

* 기본값만 사용할 수 있습니다 `{ controller = "Blog", action = "Article" }` .
* 이 경로는 항상 작업에 매핑됩니다 `BlogController.Article` .

`/Blog`, `/Blog/Article` 및 `/Blog/{any-string}` 은 블로그 경로와 일치 하는 유일한 URL 경로입니다.

위의 예제는 다음과 같습니다.

* `blog` 경로는 `default` 먼저 추가 되기 때문에 경로 보다 일치 하는 항목에 대 한 우선 순위가 높습니다.
* 는 문서 이름을 URL [의 일부로](https://developer.mozilla.org/docs/Glossary/Slug) 포함 하는 것이 일반적입니다.

> [!WARNING]
> ASP.NET Core 3.0 이상에서 라우팅은 다음과 같이 되지 않습니다.
> * *경로* 라는 개념을 정의 합니다. `UseRouting`은 경로 일치를 미들웨어 파이프라인에 추가합니다. `UseRouting`미들웨어는 앱에 정의 된 끝점 집합을 살펴보고 요청에 따라 가장 적합 한 끝점 일치를 선택 합니다.
> * 또는과 같은 확장성의 실행 순서에 대 한 보장을 제공 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 합니다.
>
>라우팅에 대 한 참조 자료에 대 한 [라우팅](xref:fundamentals/routing) 을 참조 하세요.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>기본 라우팅 순서

기존 라우팅은 앱에서 정의 된 작업과 컨트롤러의 조합만 일치 합니다. 이는 기존 경로가 중복 되는 경우를 단순화 하기 위한 것입니다.
, 및를 사용 하 여 경로를 추가 하면 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 호출 되는 순서에 따라 해당 끝점에 순서 값이 자동으로 할당 됩니다. 이전에 표시 된 경로의 일치 항목은 우선 순위가 높습니다. 규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로 보다 더 구체적 이므로 앞에 배치 해야 합니다. 와 같은 모든 경로 매개 변수를 사용 하는 [전용 기본 경로](#dcr) 를 사용 하면 `{*article}` 경로를 너무 많이 사용할 수 있습니다. 즉, 다른 경로와 일치 시키려는 url과 일치 하 게 됩니다. [](xref:fundamentals/routing#greedy) Greedy 일치를 방지 하려면 greedy 경로를 경로 테이블에 배치 합니다.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>모호한 작업 확인

두 끝점이 라우팅을 통해 일치 하는 경우 라우팅은 다음 중 하나를 수행 해야 합니다.

* 가장 적합 한 후보를 선택 합니다.
* 예외를 throw합니다.

예를 들면 다음과 같습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

위의 컨트롤러는 다음과 일치 하는 두 작업을 정의 합니다.

* URL 경로 `/Products33/Edit/17`
* 데이터 `{ controller = Products33, action = Edit, id = 17 }` 를 라우팅합니다.

MVC 컨트롤러의 일반적인 패턴은 다음과 같습니다.

* `Edit(int)` 제품을 편집 하는 폼을 표시 합니다.
* `Edit(int, Product)` 게시 된 폼을 처리 합니다.

올바른 경로를 확인 하려면:

* `Edit(int, Product)` 요청이 HTTP 인 경우이 선택 됩니다 `POST` .
* `Edit(int)`[HTTP 동사가](#verb) 다른 항목이 면이 선택 됩니다. `Edit(int)` 는 일반적으로을 통해 호출 됩니다 `GET` .

<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]` 요청의 HTTP 메서드를 기반으로 하 여 선택할 수 있도록 라우팅에는가 제공 됩니다. 는 `HttpPostAttribute` `Edit(int, Product)` 보다 더 일치 하는 항목을 만듭니다 `Edit(int)` .

와 같은 특성의 역할을 이해 하는 것이 중요 `HttpPostAttribute` 합니다. 다른 [HTTP 동사](#verb)에 대해 유사한 특성이 정의 됩니다. [기본 라우팅](#cr)에서 일반적으로 작업은 표시 양식, 제출 양식 워크플로의 일부인 경우에 동일한 동작 이름을 사용 합니다. 예를 들어 [두 개의 편집 작업 메서드 검사](xref:tutorials/first-mvc-app/controller-methods-views#get-post)를 참조 하세요.

라우팅이 최적의 후보를 선택할 수 없는 경우 일치 하는 <xref:System.Reflection.AmbiguousMatchException> 여러 끝점을 나열 하는이 throw 됩니다.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>기본 경로 이름

`"blog"` `"default"` 다음 예제에서 및 문자열은 기존 경로 이름입니다.

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

경로 이름은 경로에 논리적 이름을 지정 합니다. 명명 된 경로는 URL 생성에 사용할 수 있습니다. 명명 된 경로를 사용 하면 경로 순서가 URL 생성을 복잡 하 게 만들 때 URL 만들기가 간단해 집니다. 경로 이름은 고유한 응용 프로그램 전체에 해당 해야 합니다.

경로 이름:

* URL 일치 또는 요청 처리에는 영향을 주지 않습니다.
* URL 생성에만 사용 됩니다.

경로 이름 개념은 라우팅에서 [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)로 표시 됩니다. 용어 **경로 이름** 및 **끝점 이름**:

* 는 바꿔 사용할 수 있습니다.
* 문서와 코드에 사용 되는 항목은 설명 된 API에 따라 달라 집니다.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>REST Api에 대 한 특성 라우팅

REST Api는 특성 라우팅을 사용 하 여 응용 프로그램의 기능을 작업을 [HTTP 동사로](#verb)나타내는 리소스 집합으로 모델링 해야 합니다.

특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다. 다음 `StartUp.Configure` 코드는 REST API에 일반적 이며 다음 샘플에서 사용 됩니다.

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

위의 코드에서 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> 는 `UseEndpoints` 특성 라우트된 컨트롤러를 매핑하기 위해 내부에서 호출 됩니다.

다음 예제에서는

* 위의 `Configure` 메서드가 사용 됩니다.
* `HomeController` 기본 기본 경로와 유사한 Url 집합과 일치 `{controller=Home}/{action=Index}/{id?}` 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

`HomeController.Index`작업은 URL 경로,, 또는에 대해 실행 `/` 됩니다 `/Home` `/Home/Index` `/Home/Index/3` .

이 예에서는 특성 라우팅과 [기본 라우팅](#cr)간의 주요 프로그래밍 차이점을 강조 합니다. 특성 라우팅은 경로를 지정 하는 데 더 많은 입력이 필요 합니다. 기본 경로는 더 많은 간략하게 경로를 처리 합니다. 그러나 특성 라우팅은 각 [작업](#action)에 적용 되는 경로 템플릿을 정확 하 게 제어 하도록 허용 합니다.

특성 라우팅을 사용 하는 경우에는 [토큰 대체가](#routing-token-replacement-templates-ref-label) 사용 되지 않는 한 컨트롤러 및 작업 이름이 동작이 일치 하는 부분을 재생 하지 않습니다. 다음 예제에서는 이전 예제와 동일한 Url을 찾습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

다음 코드에서는 및에 대 한 토큰 대체를 사용 합니다 `action` `controller` .

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

다음 코드는 `[Route("[controller]/[action]")]` 컨트롤러에 적용 됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

위의 코드에서 `Index` 메서드 템플릿은 `/` 경로 템플릿에 또는 앞에와 야 합니다 `~/` . 작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다.

경로 템플릿 선택에 대 한 자세한 내용은 [경로 템플릿 우선 순위](xref:fundamentals/routing#rtp) 를 참조 하세요.

## <a name="reserved-routing-names"></a>예약된 라우팅 이름

다음 키워드는 컨트롤러 또는 페이지를 사용할 때 예약 된 경로 매개 변수 이름입니다 Razor .

* `action`
* `area`
* `controller`
* `handler`
* `page`

`page`특성 라우팅이 있는 경로 매개 변수로를 사용 하는 것은 일반적인 오류입니다. 이렇게 하면 URL 생성 시 일관 되지 않은 동작이 발생 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

특수 매개 변수 이름은 url 생성 작업에서 페이지 또는 컨트롤러를 참조 하는지 확인 하는 데 사용 됩니다 Razor .

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP 동사 템플릿

ASP.NET Core에는 다음과 같은 HTTP 동사 템플릿이 있습니다.

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>경로 템플릿

ASP.NET Core에는 다음과 같은 경로 템플릿이 있습니다.

* 모든 [HTTP 동사 템플릿은](#verb) 경로 템플릿입니다.
* [경로](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Http 동사 특성을 사용 하는 특성 라우팅

다음 컨트롤러를 살펴보십시오.

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

앞의 코드에서 다음을 확인할 수 있습니다.

* 각 작업에는 `[HttpGet]` HTTP GET 요청에 대해서만 일치를 제한 하는 특성이 포함 됩니다.
* `GetProduct`작업은 템플릿을 포함 `"{id}"` 하므로 `id` `"api/[controller]"` 컨트롤러의 템플릿에 추가 됩니다. 메서드 템플릿은 `"api/[controller]/"{id}""` 입니다. 따라서이 작업은,, 등의 폼에 대 한 GET 요청만 일치 `/api/test2/xyz` `/api/test2/123` `/api/test2/{any string}` 합니다.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* 작업에는 `GetIntProduct` 템플릿이 포함 되어 있습니다 `"int/{id:int}")` . `:int`템플릿의 부분은 `id` 정수로 변환할 수 있는 문자열에 대 한 경로 값을 제한 합니다. GET 요청 대상 `/api/test2/int/abc` :
  * 이 작업과 일치 하지 않습니다.
  * 404를 [찾을 수](https://developer.mozilla.org/docs/Web/HTTP/Status/404) 없음 오류를 반환 합니다.
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product`작업은 `{id}` 템플릿에 포함 되지만 `id` 정수로 변환할 수 있는 값으로 제한 되지 않습니다. GET 요청 대상 `/api/test2/int2/abc` :
  * 이 경로와 일치 합니다.
  * 모델 바인딩이 정수로 변환 되지 않습니다 `abc` . `id`메서드의 매개 변수는 정수입니다.
  * 모델 바인딩이 정수로 변환 하지 못해 [400 잘못 된 요청](https://developer.mozilla.org/docs/Web/HTTP/Status/400) 을 반환 합니다 `abc` .
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

특성 라우팅은 <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> , 및와 같은 특성을 사용할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> . 모든 [HTTP 동사](#verb) 특성은 경로 템플릿을 허용 합니다. 다음 예에서는 동일한 경로 템플릿과 일치 하는 두 가지 작업을 보여 줍니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

URL 경로 사용 `/products3` :

* `MyProductsController.ListProducts` [HTTP 동사가](#verb) 일 때 동작이 실행 됩니다 `GET` .
* `MyProductsController.CreateProduct` [HTTP 동사가](#verb) 일 때 동작이 실행 됩니다 `POST` .

REST API를 빌드할 때 `[Route(...)]` 작업에서 모든 HTTP 메서드를 허용 하므로 작업 메서드에서를 사용 해야 하는 경우는 드뭅니다. API에서 지 원하는 항목을 정확 하 게 이해 하려면 보다 구체적인 [HTTP 동사 특성](#verb) 을 사용 하는 것이 좋습니다. REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.

REST Api는 특성 라우팅을 사용 하 여 응용 프로그램의 기능을 작업을 HTTP 동사로 나타내는 리소스 집합으로 모델링 해야 합니다. 즉, 동일한 논리 리소스에 대 한 GET 및 POST와 같은 많은 작업은 동일한 URL을 사용 합니다. 특성 라우팅은 API의 공개 엔드포인트 레이아웃을 신중하게 설계하는 데 필요한 제어 수준을 제공합니다.

특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다. 다음 예제에서 `id` 는 URL 경로의 일부로 필요 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

`Products2ApiController.GetProduct(int)`작업은 다음과 같습니다.

* 다음과 같이 URL 경로를 사용 하 여 실행 됩니다. `/products2/3`
* URL 경로를 사용 하 여 실행 되지 않습니다 `/products2` .

[[사용]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 특성을 사용하면 작업에서 지원되는 요청 콘텐츠 형식을 제한할 수 있습니다. 자세한 내용은 [소비 특성을 사용 하 여 지원 되는 요청 콘텐츠 형식 정의](xref:web-api/index#consumes)를 참조 하세요.

 경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

에 대 한 자세한 내용은 `[ApiController]` [ApiController 특성](xref:web-api/index##apicontroller-attribute)을 참조 하세요.

## <a name="route-name"></a>경로 이름

다음 코드는 의 `Products_List`경로 이름을 정의합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다. 경로 이름:

* 라우팅의 URL 일치 동작에는 영향을 주지 않습니다.
* URL 생성에만 사용 됩니다.

경로 이름은 애플리케이션 전체에서 고유해야 합니다.

앞의 코드와 `id` 매개 변수를 선택적 ()으로 정의 하는 기존의 기본 경로를 대조 합니다 `{id?}` . Api를 정확 하 게 지정 하는 기능에는 `/products` 및 `/products/5` 를 다른 작업에 디스패치할 수 있는 것과 같은 장점이 있습니다.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>특성 경로 조합

특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다. 컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다. 경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

앞의 예제에서:

* URL 경로가 `/products` 일치 하는 경우 `ProductsApi.ListProducts`
* URL 경로는 `/products/5` 와 일치할 수 있습니다 `ProductsApi.GetProduct(int)` .

이러한 작업은 모두 `GET` 특성으로 표시 되어 있으므로 HTTP만 일치 `[HttpGet]` 합니다.

작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다. 다음 예에서는 기본 경로와 유사한 URL 경로 집합을 찾습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

다음 표에서는 `[Route]` 위의 코드에 있는 특성에 대해 설명 합니다.

| attribute               | 와 결합 `[Route("Home")]` | 경로 템플릿을 정의 합니다. |
| ----------------- | ------------ | --------- |
| `[Route("")]` | 예 | `"Home"` |
| `[Route("Index")]` | 예 | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | 예 | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>특성 경로 순서

라우팅은 트리를 빌드하고 모든 끝점을 동시에 일치 시킵니다.

* 경로 항목은 이상적인 순서로 배치 된 것 처럼 동작 합니다.
* 가장 구체적인 경로는 보다 일반적인 경로 보다 먼저 실행 될 가능성이 있습니다.

예를 들어와 같은 특성 경로는 `blog/search/{topic}` 와 같은 특성 경로 보다 구체적입니다 `blog/{*article}` . `blog/search/{topic}`경로는 보다 구체적 이므로 기본적으로 우선 순위가 높습니다. 개발자는 [기본 라우팅을](#cr)사용 하 여 원하는 순서로 경로를 배치할 책임이 있습니다.

특성 경로는 속성을 사용 하 여 주문을 구성할 수 있습니다 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> . 제공 된 모든 프레임 워크 [경로 특성](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 에는가 포함 `Order` 됩니다. 경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다. 기본 순서는 `0`입니다. 를 사용 하 여 경로를 설정 하 `Order = -1` 는 것은 순서를 설정 하지 않은 경로 보다 먼저 실행 됩니다. 기본 경로 순서 지정 후를 사용 하 여 경로를 설정 `Order = 1` 합니다.

 에 따라 방지 `Order` 합니다. 응용 프로그램의 URL 공간에 올바른 경로를 위해 명시적인 순서 값이 필요한 경우에는 클라이언트와 혼동 될 수 있습니다. 일반적으로 특성 라우팅은 URL이 일치 하는 올바른 경로를 선택 합니다. URL 생성에 사용 되는 기본 순서가 작동 하지 않는 경우 경로 이름을 재정의로 사용 하는 것은 일반적으로 속성을 적용 하는 것 보다 간단 합니다 `Order` .

경로 일치를 정의 하는 다음 두 개의 컨트롤러를 고려 하십시오 `/home` .

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

위의 코드를 요청 하면 `/home` 다음과 유사한 예외가 throw 됩니다.

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

`Order`경로 특성 중 하나에를 추가 하면 모호성을 해결 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

위의 코드를 사용 하 여 `/home` 끝점을 실행 `HomeController.Index` 합니다. 에 가져오려면를 `MyDemoController.MyIndex` 요청 `/home/MyIndex` 합니다. **고**:

* 위의 코드는 예 이거나 낮은 라우팅 디자인입니다. 속성을 설명 하는 데 사용 되었습니다 `Order` .
* `Order`속성은 모호성을 해결 하 고 해당 템플릿은 일치 시킬 수 없습니다. 템플릿을 제거 하는 것이 좋습니다 `[Route("Home")]` .

페이지 [ Razor 경로 및 앱 규칙:](xref:razor-pages/razor-pages-conventions#route-order) 경로 순서와 페이지의 경로 순서에 대 한 정보를 참조 하세요 Razor .

일부 경우에는 모호한 경로를 사용 하 여 HTTP 500 오류가 반환 됩니다. [로깅을](xref:fundamentals/logging/index) 사용 하 여를 일으킨 끝점을 확인 `AmbiguousMatchException` 합니다.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>경로 템플릿에서 토큰 바꾸기 [컨트롤러], [작업], [영역]

편의를 위해 특성 경로는 토큰을 대괄호 (,)로 묶어 *토큰 바꾸기를* 지원 `[` `]` 합니다. `[action]`, 및 토큰은 `[area]` `[controller]` 경로가 정의 된 작업에서 동작 이름, 영역 이름 및 컨트롤러 이름 값으로 대체 됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

앞의 코드에서 다음을 확인할 수 있습니다.

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * 항목 `/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * 항목 `/Products0/Edit/{id}`

토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다. 앞의 예제는 다음 코드와 동일 하 게 동작 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

특성 경로를 상속과 결합할 수도 있습니다. 토큰 교체와 강력 하 게 결합 됩니다. 토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`각 작업에 대해 고유한 경로 이름을 생성 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
는 각 작업의 고유한 경로 이름을 생성합니다.

리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>매개 변수 변환기를 사용하여 토큰 교체 사용자 지정

매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다. 매개 변수 변환기는 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer>를 구현하며 매개 변수의 값을 변환합니다. 예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을로 변경 합니다 `subscription-management` .

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention>은 다음과 같은 응용 프로그램 모델 규칙입니다.

* 애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.
* 대체되는 특성 경로 토큰 값을 사용자 지정합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

이전 `ListAll` 메서드는와 일치 `/subscription-management/list-all` 합니다.

`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

슬러그의 정의는 [슬러그의 MDN 웹 문서](https://developer.mozilla.org/docs/Glossary/Slug) 를 참조 하세요.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>여러 특성 경로

특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다. 가장 일반적인 사용 방법은 다음 예제와 같이 기본 규칙 기반 경로의 동작을 모방하는 것입니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

여러 경로 특성을 컨트롤러에 배치 하면 각 경로 특성이 동작 메서드의 각 경로 특성과 결합 됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

모든 [HTTP 동사](#verb) 경로 제약 조건은를 구현 `IActionConstraint` 합니다.

을 구현 하는 여러 경로 특성이 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 동작에 배치 되는 경우:

* 각 작업 제약 조건은 컨트롤러에 적용 된 경로 템플릿과 결합 됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

작업에 여러 경로를 사용 하면 유용 하 고 강력 하 게 보일 수 있습니다. 앱의 URL 공간을 기본 및 잘 정의 된 상태로 유지 하는 것이 좋습니다. 기존 클라이언트를 지 원하는 경우와 같이 필요한 경우에 **만** 작업에 대해 여러 경로를 사용 합니다.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정

특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

위의 코드에서는 `[HttpPost("product/{id:int}")]` 경로 제약 조건을 적용 합니다. `ProductsController.ShowProduct`작업은와 같은 URL 경로만 일치 시킵니다 `/product/3` . 경로 템플릿 부분은 `{id:int}` 해당 세그먼트를 정수로 제한 합니다.

경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>IRouteTemplateProvider를 사용 하는 사용자 지정 경로 특성

모든 [경로 특성](#rt) 은를 구현 <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> 합니다. ASP.NET Core 런타임:

* 응용 프로그램이 시작 될 때 컨트롤러 클래스 및 작업 메서드에서 특성을 찾습니다.
* 는를 구현 하는 특성을 사용 하 여 `IRouteTemplateProvider` 초기 경로 집합을 작성 합니다.

`IRouteTemplateProvider`을 구현 하 여 사용자 지정 경로 특성을 정의 합니다. 각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

이전 메서드는를 `Get` 반환 합니다 `Order = 2, Template = api/MyTestApi` .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>응용 프로그램 모델을 사용 하 여 특성 경로 사용자 지정

응용 프로그램 모델:

* 는 시작할 때 생성 되는 개체 모델입니다.
* 앱에서 작업을 라우팅하고 실행 하기 위해 ASP.NET Core에서 사용 하는 모든 메타 데이터를 포함 합니다.

응용 프로그램 모델은 경로 특성에서 수집 된 모든 데이터를 포함 합니다. 경로 특성의 데이터는 구현에 의해 제공 됩니다 `IRouteTemplateProvider` . 규약

* 응용 프로그램 모델을 수정 하 여 라우팅이 동작 하는 방식을 사용자 지정 하도록를 작성할 수 있습니다.
* 앱 시작 시 읽습니다.

이 섹션에서는 응용 프로그램 모델을 사용 하 여 라우팅을 사용자 지정 하는 기본적인 예를 보여 줍니다. 다음 코드는 프로젝트의 폴더 구조를 사용 하 여 경로를 대략적으로 만듭니다.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

다음 코드는 `namespace` 특성이 라우팅되는 컨트롤러에 규칙이 적용 되지 않도록 합니다.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

예를 들어 다음 컨트롤러는를 사용 하지 않습니다 `NamespaceRoutingConvention` .

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` 메서드는 다음 작업을 수행합니다.

* 컨트롤러에서 특성을 라우팅하는 경우 아무 작업도 수행 하지 않습니다.
* 을 기반으로 하는 컨트롤러 템플릿을 설정 합니다 `namespace` . 이때 기본가 `namespace` 제거 됩니다.

는 `NamespaceRoutingConvention` 다음에 적용할 수 있습니다 `Startup.ConfigureServices` .

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

예를 들어 다음 컨트롤러를 살펴보세요.

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

앞의 코드에서 다음을 확인할 수 있습니다.

* 밑 `namespace` 이 인 `My.Application` 경우
* 이전 컨트롤러의 전체 이름은 `My.Application.Admin.Controllers.UsersController` 입니다.
* 는 `NamespaceRoutingConvention` 컨트롤러 템플릿을로 설정 합니다 `Admin/Controllers/Users/[action]/{id?` .

를 `NamespaceRoutingConvention` 컨트롤러의 특성으로 적용할 수도 있습니다.

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅

ASP.NET Core apps는 기존 라우팅 및 특성 라우팅의 사용을 혼합할 수 있습니다. 일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.

작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다. 컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다. 특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다. 컨트롤러의 **모든** 경로 특성은 컨트롤러 특성의 모든 작업 **을 라우팅합니다.**

특성 라우팅 및 기존 라우팅은 동일한 라우팅 엔진을 사용 합니다.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL 생성 및 앰비언트 값

앱은 라우팅 URL 생성 기능을 사용 하 여 작업에 대 한 URL 링크를 생성할 수 있습니다. Url을 생성 하면 하드 코딩 되는 Url이 제거 되어 코드가 더 강력 하 고 유지 관리 됩니다. 이 섹션에서는 MVC에서 제공 하는 URL 생성 기능에 초점을 맞춘 후 URL 생성이 작동 하는 방식에 대 한 기본 내용만 다룹니다. URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper>인터페이스는 MVC와 URL 생성을 위한 라우팅 간의 기본 인프라 요소입니다. 인스턴스는 `IUrlHelper` `Url` 컨트롤러, 뷰 및 뷰 구성 요소의 속성을 통해 사용할 수 있습니다.

다음 예제에서 `IUrlHelper` 인터페이스는 속성을 통해 `Controller.Url` 다른 동작에 대 한 URL을 생성 하는 데 사용 됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

앱에서 기본 기본 경로를 사용 하는 경우 변수의 값은 `url` URL 경로 문자열입니다 `/UrlGeneration/Destination` . 이 URL 경로는 다음을 결합 하 여 라우팅하는 방식으로 만들어집니다.

* **앰비언트 값** 이라고 하는 현재 요청의 경로 값입니다.
* 에 전달 되는 값으로 `Url.Action` , 해당 값을 경로 템플릿으로 대체 합니다.

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다. 값이 없는 경로 매개 변수는 다음을 수행할 수 있습니다.

* 기본값이 있는 경우 기본값을 사용 합니다.
* 선택 사항인 경우 건너뜁니다. 예를 들어 `id` 경로 템플릿의 `{controller}/{action}/{id?}` 입니다.

필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성이 실패 합니다. 경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.

위의 예에서는 `Url.Action` [기존 라우팅을](#cr)가정 합니다. 개념은 서로 다르지만 URL 생성은 [특성 라우팅과](#ar)유사 하 게 작동 합니다. 기본 라우팅을 사용 하는 경우:

* 경로 값은 템플릿을 확장 하는 데 사용 됩니다.
* 및에 대 한 경로 값은 `controller` `action` 일반적으로 해당 템플릿에 표시 됩니다. 이는 라우팅을 통해 일치 하는 Url이 규칙을 준수 하기 때문에 작동 합니다.

다음 예에서는 특성 라우팅을 사용 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

`Source`위의 코드에서 작업은을 생성 `custom/url/to/destination` 합니다.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 는의 대 안으로 ASP.NET Core 3.0에 추가 되었습니다 `IUrlHelper` . `LinkGenerator` 는 유사 하지만 유연한 기능을 제공 합니다. 의 각 메서드에 `IUrlHelper` 는의 해당 메서드 패밀리가 있습니다 `LinkGenerator` .

### <a name="generating-urls-by-action-name"></a>작업 이름으로 URL 생성

[Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [Linkgenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)및 모든 관련 된 오버 로드는 컨트롤러 이름과 작업 이름을 지정 하 여 대상 끝점을 생성 하도록 디자인 되었습니다.

를 사용 하는 경우 `Url.Action` 및에 대 한 현재 경로 값은 `controller` `action` 런타임에서 제공 됩니다.

* 및의 값 `controller` 은 `action` [앰비언트 값](#ambient) 과 값의 일부입니다. 메서드는 `Url.Action` 항상 및의 현재 값을 사용 하 `action` `controller` 고 현재 작업으로 라우팅되는 URL 경로를 생성 합니다.

라우팅은 앰비언트 값의 값을 사용 하 여 URL을 생성할 때 제공 되지 않은 정보를 채우도록 시도 합니다. 앰비언트 값과 같은 경로를 고려 합니다 `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` .

* 라우팅에는 추가 값 없이 URL을 생성 하는 데 충분 한 정보가 있습니다.
* 모든 경로 매개 변수에는 값이 있으므로 라우팅에 충분 한 정보가 있습니다.

값 `{ d = Donovan }` 이 추가 된 경우:

* 값은 `{ d = David }` 무시 됩니다.
* 생성 된 URL 경로는 `Alice/Bob/Carol/Donovan` 입니다.

**경고**: URL 경로는 계층 구조입니다. 앞의 예제에서 다음과 같이 값을 `{ c = Cheryl }` 추가 합니다.

* 두 값은 모두 `{ c = Carol, d = David }` 무시 됩니다.
* 에 대 한 값이 더 이상 없으며 `d` URL 생성이 실패 합니다.
* `c`URL을 생성 하려면 및의 원하는 값을 `d` 지정 해야 합니다.  

기본 경로를 사용 하 여이 문제가 발생할 수 있습니다 `{controller}/{action}/{id?}` . `Url.Action`항상 명시적으로 및 값을 지정 하기 때문에이 문제는 드물게 발생 `controller` `action` 합니다.

Url의 여러 오버 로드. Action은 경로 값 개체를 사용 하 여 및 이외의 경로 매개 변수에 대 한 값을 제공 [합니다.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) `controller` `action` 경로 값 개체는와 함께 자주 사용 됩니다 `id` . 예들 들어 `Url.Action("Buy", "Products", new { id = 17 })`입니다. 경로 값 개체:

* 규칙에 따라 일반적으로 무명 형식의 개체입니다.
* `IDictionary<>`또는 [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)일 수 있습니다.

경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

위의 코드는을 생성 `/Products/Buy/17?color=red` 합니다.

다음 코드에서는 절대 URL을 생성 합니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

절대 URL을 만들려면 다음 중 하나를 사용 합니다.

* 을 허용 하는 오버 로드 `protocol` 입니다. 예를 들어 앞의 코드입니다.
* [Linkgenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)은 기본적으로 절대 uri를 생성 합니다.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>경로로 Url 생성

위의 코드는 컨트롤러 및 작업 이름을 전달 하 여 URL을 생성 하는 방법을 보여 주었습니다. `IUrlHelper` 또한 [RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) 메서드 패밀리를 제공 합니다. 이러한 메서드는 [Url](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)과 유사 하지만 및의 현재 값을 `action` 경로 값에 복사 하지 않습니다 `controller` . 의 가장 일반적인 사용법은 `Url.RouteUrl` 다음과 같습니다.

* URL을 생성 하는 경로 이름을 지정 합니다.
* 일반적으로 컨트롤러 또는 작업 이름을 지정 하지 않습니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

다음 Razor 파일은에 대 한 HTML 링크를 생성 합니다 `Destination_Route` .

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a>HTML로 Url 생성 Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>및 요소를 각각 생성 하는 [](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) [html.beginform](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) 및 html.actionlink 메서드를 제공 합니다 `<form>` `<a>` . 이러한 메서드는 url을 생성 하는 데 [url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 메서드를 사용 하 여 비슷한 인수를 허용 합니다. `HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.

TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다. 둘 다 구현에 `IUrlHelper`를 사용합니다. 자세한 내용은 [양식의 태그 도우미](xref:mvc/views/working-with-forms) 를 참조 하세요.

보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>작업 결과의 URL 생성

위의 예제는 컨트롤러에서를 사용 하는 방법을 보여 주었습니다 `IUrlHelper` . 컨트롤러에서 가장 일반적으로 사용 되는 작업은 URL을 작업 결과의 일부로 생성 하는 것입니다.

<xref:Microsoft.AspNetCore.Mvc.ControllerBase> 및 <xref:Microsoft.AspNetCore.Mvc.Controller> 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다. 일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

및와 같은 작업 결과 팩터리 메서드 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> 는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> 의 메서드와 유사한 패턴을 따릅니다 `IUrlHelper` .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>전용 규칙 기반 경로의 특별한 사례

[기존 라우팅은](#cr) [전용 기본 경로](#dcr)라고 하는 특별 한 종류의 경로 정의를 사용할 수 있습니다. 다음 예제에서 라는 경로는 `blog` 전용 기본 경로입니다.

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

위의 경로 정의를 사용 하 여 `Url.Action("Index", "Home")` 경로를 사용 하 여 URL 경로를 생성 `/` `default` 하지만 이유는 무엇입니까? `{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.

[전용 기본 경로](#dcr) 는 URL 생성을 사용 하 여 경로가 너무 [non-greedy](xref:fundamentals/routing#greedy) 하는 것을 방지 하는 해당 경로 매개 변수가 없는 기본 값의 특수 한 동작을 사용 합니다. 이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다. 라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다. `blog`값이 일치 하지 않으므로를 사용한 URL 생성이 실패 `{ controller = Home, action = Index }` `{ controller = Blog, action = Article }` 합니다. 그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>영역

[영역은](xref:mvc/controllers/areas) 별도의 그룹으로 관련 기능을 구성 하는 데 사용 되는 MVC 기능입니다.

* 컨트롤러 작업에 대 한 라우팅 네임 스페이스입니다.
* 보기에 대 한 폴더 구조입니다.

영역을 사용 하면 서로 다른 영역이 있는 한 앱에 동일한 이름의 여러 컨트롤러가 있을 수 있습니다. 영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다. 이 섹션에서는 라우팅이 영역과 상호 작용 하는 방법에 대해 설명 합니다. 영역을 뷰에서 사용 하는 방법에 대 한 자세한 내용은 [영역](xref:mvc/controllers/areas) 을 참조 하세요.

다음 예에서는 기본 기본 경로와 명명 된의 경로를 사용 하도록 MVC를 구성 합니다 `area` `area` `Blog` .

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

위의 코드에서를 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 만들기 위해가 호출 됩니다 `"blog_route"` . 두 번째 매개 변수인는 `"Blog"` 영역 이름입니다.

URL 경로와 일치 하는 경우 경로 `/Manage/Users/AddUser` 는 `"blog_route"` 경로 값을 생성 합니다 `{ area = Blog, controller = Users, action = AddUser }` . `area`경로 값은에 대 한 기본값으로 생성 됩니다 `area` . 에서 만든 경로는 `MapAreaControllerRoute` 다음과 같습니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다. 기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.

규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로 보다 더 구체적 이므로 앞에 배치 해야 합니다.

위의 예제를 사용 하면 경로 값이 `{ area = Blog, controller = Users, action = AddUser }` 다음 동작과 일치 합니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) 특성은 컨트롤러를 영역의 일부로 나타내는 것입니다. 이 컨트롤러는 영역에 `Blog` 있습니다. 특성이 없는 컨트롤러 `[Area]` 는 어떤 영역의 구성원도 아니므로  `area` 경로 값이 라우팅에서 제공 되는 경우 일치 하지 않습니다. 다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

각 컨트롤러의 네임 스페이스는 완전성을 위해 여기에 표시 됩니다. 이전 컨트롤러에서 동일한 네임 스페이스를 사용 하는 경우 컴파일러 오류가 생성 됩니다. 클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.

처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다. 세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.

<a name="aa"></a>

매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.

영역 내에서 작업을 실행 하는 경우의 경로 값은 `area` URL 생성에 사용할 라우팅에 대 한 [앰비언트 값](#ambient) 으로 사용할 수 있습니다. 즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

다음 코드에서는에 대 한 URL을 생성 합니다 `/Zebra/Users/AddUser` .

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>작업 정의

[비 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) 특성을 사용 하는 경우를 제외 하 고 컨트롤러의 공용 메서드는 동작입니다.

## <a name="sample-code"></a>예제 코드

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* [예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([다운로드 방법](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC는 라우팅 [미들웨어](xref:fundamentals/middleware/index)를 사용하여 들어오는 요청의 URL을 매칭하고 작업에 매핑합니다. 경로는 시작 코드 또는 특성에서 정의됩니다. 경로는 URL 경로를 작업에 매칭하는 방법을 설명합니다. 경로는 응답으로 전송되는 URL(링크 용)을 생성하기 위해서도 사용됩니다.

작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다. 컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다. 자세한 내용은 [혼합 라우팅](#routing-mixed-ref-label)을 참조하세요.

이 항목에서는 MVC와 라우팅 간의 상호 작용 및 일반적인 MVC 앱이 라우팅 기능을 사용하는 방식에 관해서 설명합니다. 고급 라우팅에 대한 자세한 내용은 [라우팅](xref:fundamentals/routing)을 참조하세요.

## <a name="setting-up-routing-middleware"></a>라우팅 미들웨어 설정

*Configure* 메서드에서 다음과 비슷한 코드를 볼 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` 호출 내부에서 `MapRoute`를 사용하여 `default` 경로라고 부르는 단일 경로를 생성합니다. 대부분의 MVC 앱은 `default` 경로와 비슷한 템플릿을 갖는 경로를 사용합니다.

`"{controller=Home}/{action=Index}/{id?}"` 경로 템플릿은 `/Products/Details/5`와 같은 URL 경로를 매칭하고 경로를 토큰화하여 `{ controller = Products, action = Details, id = 5 }` 경로 값을 추출합니다. MVC는 `ProductsController`라는 컨트롤러를 찾아 `Details` 작업을 실행하려고 시도합니다.

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

이 예제에서 이 작업을 호출할 때 모델 바인딩이 `id = 5` 값을 사용하여 `id` 매개 변수를 `5`로 설정합니다. 자세한 내용은 [모델 바인딩](../models/model-binding.md)을 참조하세요.

`default` 경로를 사용하여:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

경로 템플릿의:

* `{controller=Home}`는 `Home`를 기본 `controller`으로 정의합니다.

* `{action=Index}`는 `Index`를 기본 `action`으로 정의합니다.

* `{id?}`는 `id`를 선택 사항으로 정의합니다.

기본 및 선택적 경로 매개 변수는 매칭을 위해 URL 경로에 반드시 있어야 하는 것은 아닙니다. 경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.

`"{controller=Home}/{action=Index}/{id?}"`는 URL 경로 `/`를 매칭할 수 있으며 `{ controller = Home, action = Index }` 경로 값을 생성합니다. `controller` 및 `action` 값으로 기본값이 사용되며, `id`는 URL 경로에 해당 세그먼트가 없기 때문에 값을 생성하지 않습니다. MVC는 이러한 경로 값을 사용하여 `HomeController` 및 `Index` 작업을 선택합니다.

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

이러한 컨트롤러 정의와 경로 템플릿을 사용하면 다음 URL 경로에 대해 `HomeController.Index` 작업이 실행됩니다.

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

편의 메서드인 `UseMvcWithDefaultRoute`를 사용하여:

```csharp
app.UseMvcWithDefaultRoute();
```

다음을 대체할 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` 및 `UseMvcWithDefaultRoute`는 미들웨어 파이프라인에 `RouterMiddleware` 인스턴스를 추가합니다. MVC는 미들웨어와 직접 상호 작용하지 않고 라우팅을 사용하여 요청을 처리합니다. MVC는 `MvcRouteHandler`의 인스턴스를 통해 경로에 연결됩니다. `UseMvc` 내부의 코드는 다음과 비슷합니다.

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`는 아무런 경로도 직접 정의하지 않고 경로 컬렉션에 `attribute` 경로에 대한 자리 표시자만 추가합니다. `UseMvc(Action<IRouteBuilder>)` 오버로드를 사용하면 고유의 경로를 추가하고 특성 라우팅도 지원할 수 있습니다.  `UseMvc` 및 모든 변형은 특성 경로에 대한 자리 표시자를 추가합니다. 특성 라우팅은 `UseMvc`를 구성하는 방법에 관계없이 항상 사용할 수 있습니다. `UseMvcWithDefaultRoute`는 기본 경로를 정의하고 특성 라우팅을 지원합니다. [특성 라우팅](#attribute-routing-ref-label) 섹션에는 특성 라우팅에 대한 자세한 내용이 포함되어 있습니다.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>규칙 기반 라우팅

`default` 경로인:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

위의 코드는 기존 라우팅의 예입니다. 이 스타일은 URL 경로에 대 한 *규칙* 을 설정 하기 때문에 기존 라우팅 이라고 합니다.

* 첫 번째 경로 세그먼트는 컨트롤러 이름에 매핑됩니다.
* 두 번째는 작업 이름에 매핑됩니다.
* 세 번째 세그먼트는 선택 사항에 사용 됩니다 `id` . `id` 모델 엔터티에 매핑됩니다.

이 `default` 경로를 사용하면 URL 경로 `/Products/List`는 `ProductsController.List` 작업에 매핑되고 `/Blog/Article/17`은 `BlogController.Article`에 매핑됩니다. 매핑은 **오직** 컨트롤러 및 작업 이름만을 기준으로 하며 네임스페이스, 원본 파일 위치 또는 메서드 매개 변수를 기준으로 하지 않습니다.

> [!TIP]
> 기본 경로와 함께 규칙 기반 라우팅을 사용하면 정의하는 각 작업마다 새 URL 패턴을 만들지 않고도 신속하게 애플리케이션을 만들 수 있습니다. CRUD 스타일의 작업을 수행하는 애플리케이션의 경우 컨트롤러 전반에서 URL을 일관적으로 유지하면 코드를 단순화하고 UI의 예측 가능성을 높이는 데 도움이 됩니다.

> [!WARNING]
> `id`는 경로 템플릿에서 선택 사항으로 정의됩니다. 즉, URL의 일부로 제공되는 ID 없이도 작업을 실행할 수 있습니다. 일반적으로 URL에서 `id`가 생략되면 모델 바인딩에 의해 `0`으로 설정되고, 그 결과 데이터베이스에서 `id == 0`과 일치하는 엔터티를 찾을 수 없습니다. 특성 라우팅을 사용하면 일부 작업에는 필요하고 다른 작업에는 필요하지 않은 ID를 만들기 위해 세밀하게 제어할 수 있습니다. 일반적으로 `id` 같은 선택적 매개 변수가 올바른 사용법으로 나타날 가능성이 있는 경우 설명서에 이러한 선택적 매개 변수가 포함됩니다.

## <a name="multiple-routes"></a>여러 경로

`MapRoute`에 대한 더 많은 호출을 추가하여 `UseMvc` 내부에서 여러 경로를 추가할 수 있습니다. 이렇게 하면 여러 규칙을 정의하거나 다음과 같이 특정 작업에만 사용되는 규칙 기반 경로를 추가할 수 있습니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

여기서 `blog` 경로는 규칙 기반 라우팅 시스템을 사용하지만 특정 작업에만 활용되는 *전용 규칙 기반 경로* 입니다. `controller` 및 `action`이 경로 템플릿에 매개 변수로 표시되지 않기 때문에 기본값만 가질 수 있으며, 따라서 이 경로는 항상 `BlogController.Article` 작업에 매핑됩니다.

경로 컬렉션의 경로는 순서대로 정렬되며 추가된 순서대로 처리됩니다. 따라서 이 예제의 `blog` 경로는 `default` 경로보다 먼저 시도됩니다.

> [!NOTE]
> *전용 기본 경로* 는 URL 경로의 나머지 부분을 캡처하는 등 **의 모든** 경로 매개 변수를 사용 하는 경우가 많습니다 `{*article}` . 이 경우 경로가 '너무 많은 욕심'을 부리게 됩니다. 즉, 다른 경로와 매칭하려는 URL과 매칭됩니다. 이 '욕심 많은' 경로를 경로 테이블의 뒷부분에 배치하면 이 문제를 해결할 수 있습니다.

### <a name="fallback"></a>대체

요청 처리의 일부로, MVC는 경로 값을 사용하여 애플리케이션의 컨트롤러 및 작업을 찾을 수 있는지 확인합니다. 경로 값이 작업과 일치하지 않으면 해당 경로는 불일치하는 것으로 간주되고, 그 다음 경로를 시도합니다. 이것을 *대체* 라고 부르며, 규칙 기반 경로가 겹치는 상황을 단순화하는 것이 목적입니다.

### <a name="disambiguating-actions"></a>명확한 작업 구분

두 작업이 라우팅을 통해 일치하는 경우 MVC는 작업을 명확히 구분하여 '최적의' 후보를 선택해야 하며, 그렇지 못하면 예외가 throw됩니다. 예를 들면 다음과 같습니다.

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

이 컨트롤러는 URL 경로 `/Products/Edit/17` 및 경로 데이터 `{ controller = Products, action = Edit, id = 17 }`과 일치하는 두 작업을 정의합니다. 이는 MVC 컨트롤러의 일반적인 패턴으로, `Edit(int)`는 제품을 편집할 양식을 보여주고 `Edit(int, Product)`는 게시된 양식을 처리합니다. 이것이 가능하려면 MVC가 요청이 HTTP `POST`이면 `Edit(int, Product)`를 선택해야 하고, HTTP 동사가 그 외의 다른 것이면 `Edit(int)`를 선택해야 합니다.

`HttpPostAttribute`(`[HttpPost]`)는 HTTP 동사가 `POST`인 경우에만 작업을 선택하는 것이 가능한 `IActionConstraint`의 구현입니다. `IActionConstraint`가 존재하면 `Edit(int, Product)`가 `Edit(int)`보다 '더 정확하게' 일치하므로 `Edit(int, Product)`를 가장 먼저 시도합니다.

개발자는 특별한 시나리오의 사용자 지정 `IActionConstraint` 구현을 작성하기만 하면 되지만, `HttpPostAttribute` 같은 특성 역할을 이해하는 것이 중요합니다. 다른 HTTP 동사에 대해 비슷한 특성이 정의됩니다. 규칙 기반 라우팅에서는 작업이 `show form -> submit form` 워크플로의 일부인 경우 작업에서 동일한 작업을 사용하는 것이 일반적입니다. [IActionConstraint 이해](#understanding-iactionconstraint) 섹션을 검토하시면 이 패턴의 편리함을 보다 명확하게 이해할 수 있습니다.

여러 경로가 일치하고 MVC가 '최적의' 경로를 찾을 수 없는 경우 MVC는 `AmbiguousActionException`을 던집니다.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>경로 이름

다음 예제의 `"blog"` 및 `"default"` 문자열은 경로 이름입니다.

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

경로 이름은 명명된 경로를 URL 생성에 사용할 수 있도록 경로에 논리 이름을 제공합니다. 이렇게 하면 경로 순서 지정으로 인해 URL 생성이 복잡해질 수 있는 상황에서 URL 생성 방법이 매우 간단해집니다. 경로 이름은 애플리케이션 전체에서 고유해야 합니다.

경로 이름은 URL 일치 또는 요청 처리에 영향을 미치지 않으며 URL 생성에서만 사용됩니다. [라우팅](xref:fundamentals/routing)은 MVC 관련 도우미에서 URL 생성을 포함하여 URL 생성에 대한 보다 자세한 정보를 갖고 있습니다.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>특성 라우팅

특성 라우팅은 특성 모음을 사용하여 작업을 경로 템플릿에 직접 매핑합니다. 다음 예제에서는 `Configure` 메서드에서 `app.UseMvc();`가 사용되어 경로가 전달되지 않습니다. `HomeController`는 기본 경로 `{controller=Home}/{action=Index}/{id?}`의 일치 항목과 비슷한 URL 집합과 매핑됩니다.

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

`HomeController.Index()` 작업은 URL 경로 `/`, `/Home` 또는 `/Home/Index`에 대해서 실행됩니다.

> [!NOTE]
> 이 예제에서는 특성 라우팅과 규칙 기반 라우팅 간의 주요 프로그래밍 차이점을 강조합니다. 특성 라우팅은 경로를 지정하기 위해 더 많은 입력이 필요하고, 규칙 기반 기본 경로는 경로를 보다 간결하게 처리합니다. 그러나 특성 라우팅을 사용하면 각 작업에 적용되는 경로 템플릿을 정확하게 제어할 수 있습니다(또 그래야만 합니다).

특성 라우팅을 사용하면 컨트롤러 이름 및 작업 이름은 작업 선택에 있어서 아무 역할도 하지 **않습니다**. 이 예제는 이전 예제와 동일한 URL을 매칭합니다.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> 위의 경로 템플릿은 `action`, `area` 및 `controller`에 대한 경로 매개 변수를 정의하지 않습니다. 사실, 이러한 경로 매개 변수는 특성 경로에 허용되지 않습니다. 경로 템플릿은 이미 작업에 연결되어 있기 때문에 URL에서 작업 이름을 구문 분석할 수 없습니다.

## <a name="attribute-routing-with-httpverb-attributes"></a>Http[Verb] 특성을 사용한 특성 라우팅

특성 라우팅은 `HttpPostAttribute` 같은 `Http[Verb]`를 사용할 수도 있습니다. 이러한 특성은 모두 경로 템플릿을 허용합니다. 이 예제는 동일한 경로 템플릿과 일치하는 두 가지 작업을 보여줍니다.

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

`/products` 같은 URL 경로의 경우 HTTP 동사가 `GET`이면 `ProductsApi.ListProducts` 작업이 실행되고 HTTP 동사가 `POST`이면 `ProductsApi.CreateProduct`가 실행됩니다. 특성 라우팅은 먼저 URL을 경로 특성에 정의된 경로 템플릿 집합과 매칭합니다. 경로 템플릿이 일치하면 `IActionConstraint` 제약 조건을 적용하여 실행 가능한 작업을 확인합니다.

> [!TIP]
> REST API를 빌드할 때 작업 메서드에 `[Route(...)]`를 사용하려는 경우는 거의 없습니다. 작업이 모든 HTTP 메서드를 받기 때문입니다. 보다 구체적인 `Http*Verb*Attributes`를 사용하여 API에서 지원하는 항목을 정확하게 지정하는 것이 좋습니다. REST API의 클라이언트는 특정 논리 작업에 어떤 경로 및 HTTP 동사가 매핑되는지 알아야 합니다.

특성 경로는 특정 작업에 적용되므로 경로 템플릿 정의의 일환으로 필요한 매개 변수를 간단하게 만들 수 있습니다. 이 예제에서 `id`는 URL 경로에 포함되어야 합니다.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` 작업은 `/products/3` 같은 URL 경로에 대해 실행되지만 `/products` 같은 URL 경로에 대해서는 실행되지 않습니다. 경로 템플릿 및 관련 옵션에 대한 전체 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

## <a name="route-name"></a>경로 이름

다음 코드는 `Products_List`의 *경로 이름* 을 정의합니다.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

경로 이름을 사용하여 특정 경로 기반의 URL을 생성할 수 있습니다. 경로 이름은 라우팅의 URL 매칭 동작에 영향을 미치지 않으며 URL 생성에만 사용됩니다. 경로 이름은 애플리케이션 전체에서 고유해야 합니다.

> [!NOTE]
> `id` 매개 변수를 선택 사항(`{id?}`)으로 정의하는 규칙 기반 *기본 경로* 와는 대조적입니다. API를 정확하게 지정하는 이 기능은 `/products`와 `/products/5`를 서로 다른 작업에 디스패치할 수 있는 등의 장점이 있습니다.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>경로 결합

특성 라우팅의 반복 횟수를 줄이기 위해 컨트롤러의 경로 특성은 개별 작업의 경로 특성과 결합됩니다. 컨트롤러에 정의된 경로 템플릿은 작업의 경로 템플릿에 앞에 추가됩니다. 경로 특성을 컨트롤러에 배치하면 컨트롤러의 **모든** 작업이 특성 라우팅을 사용합니다.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

이 예제에서 URL 경로 `/products`는 `ProductsApi.ListProducts`와 매칭할 수 있고, URL 경로 `/products/5`는 `ProductsApi.GetProduct(int)`와 매칭할 수 있습니다. 두 작업은 모두 `HttpGetAttribute`로 표시되기 때문에 HTTP `GET`만 매칭합니다.

작업에 적용된 `/` 또는 `~/`로 시작하는 경로 템플릿은 컨트롤러에 적용된 경로 템플릿과 결합되지 않습니다. 이 예제는 *기본 경로* 와 비슷한 URL 경로 집합을 매칭합니다.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>특성 경로 순서 지정

정의 된 순서로 실행 되는 기존 경로와 달리 특성 라우팅은 트리를 작성 하 고 모든 경로를 동시에 일치 시킵니다. 이 동작은 경로 전체가 이상적인 순서로 정렬된 것처럼 수행됩니다. 가장 구체적인 경로가 일반적인 경로보다 먼저 실행될 가능성이 있습니다.

예를 들어 `blog/search/{topic}` 같은 경로는 `blog/{*article}` 같은 경로보다 구체적입니다. 논리적으로 말해서, 기본적으로 `blog/search/{topic}` 경로가 가장 먼저 '실행'됩니다. 유일하게 합리적인 순서이기 때문입니다. 규칙 기반 라우팅을 사용하면 개발자가 원하는 순서대로 경로를 배치해야 합니다.

특성 경로는 프레임워크가 제공하는 모든 경로 특성의 `Order` 속성을 사용하여 순서를 구성할 수 있습니다. 경로는 `Order` 속성의 오름차순 정렬에 따라 처리됩니다. 기본 순서는 `0`입니다. `Order = -1`을 사용한 경로 설정은 순서를 설정하지 않는 경로보다 먼저 실행됩니다. `Order = 1`을 사용한 경로 설정은 기본 경로 순서보다 늦게 실행됩니다.

> [!TIP]
> `Order`를 사용하지 마세요. URL 공간에 올바른 라우팅을 위한 명시적 순서 값이 필요한 경우 클라이언트에서도 혼란이 발생할 수 있습니다. 일반적으로 특성 라우팅은 URL이 일치하는 올바른 경로를 선택합니다. URL 생성에 사용되는 기본 순서가 작동하지 않는 경우 일반적으로 경로 이름을 재정의로 사용하는 것이 `Order` 속성을 적용하는 것보다 간단합니다.

Razor Pages 라우팅과 MVC 컨트롤러 라우팅은 구현을 공유합니다. 페이지 항목의 경로 순서에 대 한 정보 Razor 는 [ Razor 페이지 경로 및 앱 규칙: 경로 순서](xref:razor-pages/razor-pages-conventions#route-order)에서 사용할 수 있습니다.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>경로 템플릿에서 토큰 바꾸기([컨트롤러] [작업] [지역])

편의를 위해 특성 경로는 토큰을 대괄호 (,)로 묶어 *토큰 바꾸기를* 지원 `[` `]` 합니다. `[action]`, `[area]` 및 `[controller]` 토큰은 경로가 정의된 작업의 작업 이름, 영역 이름 및 컨트롤러 이름으로 바뀝니다. 다음 예의 작업은 주석에 설명된 대로 URL 경로를 매칭합니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

토큰 교체는 특성 경로 빌드 과정의 마지막 단계로 발생합니다. 위의 예제는 다음 코드와 동일하게 동작합니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

특성 경로를 상속과 결합할 수도 있습니다. 특히 토큰 교체와 결합하면 더욱 강력합니다.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

토큰 교체는 특성 경로에 정의된 경로 이름에도 적용됩니다. `[Route("[controller]/[action]", Name="[controller]_[action]")]`는 각 작업의 고유한 경로 이름을 생성합니다.

리터럴 토큰 교체 구분 기호 `[` 또는 `]`와 매칭하려면 문자(`[[` 또는 `]]`)를 반복하여 이스케이프합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>매개 변수 변환기를 사용하여 토큰 교체 사용자 지정

매개 변수 변환기를 사용하여 토큰 교체를 사용자 지정할 수 있습니다. 매개 변수 변환기는 `IOutboundParameterTransformer`를 구현하며 매개 변수의 값을 변환합니다. 예를 들어 사용자 지정 `SlugifyParameterTransformer` 매개 변수 변환기는 `SubscriptionManagement` 경로 값을 `subscription-management`로 변경합니다.

`RouteTokenTransformerConvention`은 다음과 같은 응용 프로그램 모델 규칙입니다.

* 애플리케이션의 모든 특성 경로에 매개 변수 변환기를 적용합니다.
* 대체되는 특성 경로 토큰 값을 사용자 지정합니다.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention`은 `ConfigureServices`에 옵션으로 등록됩니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>여러 경로

특성 경로는 동일한 작업에 도달하는 여러 경로를 정의하는 것을 지원합니다. 가장 일반적인 사용 방법은 다음 예제와 같이 *기본 규칙 기반 경로* 의 동작을 모방하는 것입니다.

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

컨트롤러에 여러 경로 특성을 배치하면 각 경로 특성이 작업 메서드의 각 경로 특성과 결합됩니다.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

여러 경로 특성(`IActionConstraint`를 구현하는)이 작업에 배치되면 각 작업 제약 조건은 제약 조건을 정의한 특성의 경로 템플릿과 결합됩니다.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> 작업에 여러 경로를 사용하는 것이 좋아 보일 수 있지만, 애플리케이션의 URL 공간을 간단하고 잘 정의된 상태로 유지하는 것이 좋습니다. 기존 클라이언트를 지원하려는 경우처럼 꼭 필요한 경우에만 작업에 여러 경로를 사용합니다.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>특성 경로 선택적 매개 변수, 기본값 및 제약 조건 지정

특성 경로는 선택적 매개 변수, 기본값 및 제약 조건을 지정할 수 있는 규칙 기반 경로와 동일한 인라인 구문을 지원합니다.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

경로 템플릿 구문에 대한 자세한 설명은 [경로 템플릿 참조](xref:fundamentals/routing#route-template-reference)를 참조하세요.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>`IRouteTemplateProvider`를 사용하는 사용자 지정 경로 특성

프레임워크에서 제공하는 모든 경로 특성(`[Route(...)]`, `[HttpGet(...)]` 등)은 `IRouteTemplateProvider` 인터페이스를 구현합니다. 앱이 시작되면 MVC는 컨트롤러 클래스 및 작업 메서드에서 특성을 찾아 `IRouteTemplateProvider`를 구현하는 특성을 사용하여 초기 경로 집합을 빌드합니다.

개발자 고유의 경로 특성을 정의하는 `IRouteTemplateProvider`를 구현할 수 있습니다. 각각의 `IRouteTemplateProvider`를 사용하여 사용자 지정 경로 템플릿, 순서 및 이름으로 단일 경로를 정의할 수 있습니다.

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

위의 예제의 특성은 `[MyApiController]`가 적용되면 자동으로 `Template`을 `"api/[controller]"`로 설정합니다.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>응용 프로그램 모델을 사용하여 특성 경로 사용자 지정

*응용 프로그램 모델* 은 MVC에 의해서 작업을 라우팅하고 실행하는 데 사용되는 모든 메타데이터를 이용하여 시작 시 만들어지는 개체 모델입니다. *응용 프로그램 모델* 은 `IRouteTemplateProvider`를 통해서 경로 특성으로부터 수집한 모든 데이터를 포함합니다. 개발자는 시작 시에 응용 프로그램 모델을 수정하는 *규칙* 을 작성하여 라우팅 동작을 사용자 지정할 수 있습니다. 이 섹션에서는 애플리케이션 모델을 사용하여 라우팅을 사용자 지정하는 간단한 예를 보여줍니다.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>혼합 라우팅: 특성 라우팅 및 규칙 기반 라우팅

MVC 애플리케이션은 규칙 기반 라우팅과 특성 라우팅을 혼합해서 사용할 수 있습니다. 일반적으로 브라우저의 HTML 페이지를 처리하는 컨트롤러에는 규칙 기반 경로를 사용하고 REST API를 제공하는 컨트롤러에는 특성 라우팅을 사용합니다.

작업은 규약 기반으로 라우팅되거나 특성 라우팅됩니다. 컨트롤러 또는 작업에 경로를 배치하면 해당 경로가 특성 라우팅됩니다. 특성 경로를 정의하는 동작은 규칙 기반 경로를 통해 도달할 수 없으며 그 반대도 마찬가지입니다. 컨트롤러의 **모든** 경로 특성은 컨트롤러에 있는 모든 작업에서 특성 라우팅을 사용하게 만듭니다.

> [!NOTE]
> 두 라우팅 시스템 간의 차이는 URL이 경로 템플릿과 일치한 후 적용되는 프로세스입니다. 규칙 기반 라우팅에서는 일치 항목의 경로 값을 사용하여 모든 규칙 기반 라우팅된 작업의 조회 테이블에서 작업 및 컨트롤러를 선택합니다. 특성 라우팅에서 각 템플릿은 이미 작업과 연결되어 있으며, 더 이상의 조회가 필요 없습니다.

## <a name="complex-segments"></a>복잡한 세그먼트

복잡한 세그먼트(예: `[Route("/dog{token}cat")]`)는 non-greedy 방식으로 오른쪽에서 왼쪽으로 리터럴을 매칭하여 처리됩니다. 자세한 내용은 [소스 코드](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)를 참조하세요. 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/8197)를 참조 하세요.

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL 생성

MVC 애플리케이션은 라우팅의 URL 생성 기능을 사용하여 작업에 대한 URL 링크를 생성할 수 있습니다. URL을 생성하면 하드 코딩된 URL이 제거되어 코드를 더욱 견고하게 유지할 수 있습니다. 이 섹션에서는 MVC에서 제공하는 URL 생성 기능을 중심으로 기본적인 URL 생성 원리에 대해서만 다룰 것입니다. URL 생성에 대한 자세한 설명은 [라우팅](xref:fundamentals/routing)을 참조하세요.

`IUrlHelper` 인터페이스는 URL 생성을 위한 MVC와 라우팅 간의 기본 인프라입니다. 컨트롤러, 보기 및 보기 구성 요소의 `Url` 속성을 통해서 사용 가능한 `IUrlHelper`의 인스턴스를 찾을 수 있습니다.

이 예제에서 `IUrlHelper` 인터페이스는 `Controller.Url` 속성을 통해 다른 작업에 대한 URL을 생성하는 데 사용됩니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

응용 프로그램이 기본 규칙 기반 경로를 사용할 경우 `url` 변수의 값은 URL 경로 문자열 `/UrlGeneration/Destination`입니다. 이 URL 경로는 현재 요청의 경로 값(앰비언트 값)을 `Url.Action`에 전달된 값과 결합하고 그 값을 경로 템플릿에 대체하여 생성됩니다.

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

경로 템플릿에 있는 각 경로 매개 변수의 값은 이름을 값 및 앰비언트 값과 매칭한 값으로 바뀝니다. 값이 없는 경로 매개 변수는 기본값이 있는 경우 기본값을 사용할 수 있고, 값이 선택 사항인 경우 건너뛰어도 됩니다(이 예제의 `id`처럼). 필요한 경로 매개 변수에 해당 값이 없는 경우 URL 생성에 실패합니다. 경로에 대한 URL 생성이 실패하면 모든 경로를 시도하거나 일치 항목을 찾을 때까지 그 다음 경로를 시도합니다.

위의 `Url.Action`예제는 규칙 기반 라우팅을 가정합니다. 개념은 다르지만 URL 생성 원리는 특성 라우팅과 비슷하게 동작합니다. 규칙 기반 라우팅에서 경로 값은 템플릿을 확장하는 데 사용되고, 일반적으로`controller` 및 `action`의 경로 값은 해당 템플릿에 표시됩니다. 라우팅에서 매핑된 URL이 *규칙* 을 따르기 때문입니다. 특성 라우팅에서는 `controller` 및 `action`의 경로 값이 템플릿에 표시되지 않는 대신, 사용할 템플릿을 조회하는 데 사용됩니다.

이 예제는 특성 라우팅을 사용합니다.

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC는 모든 특성 라우팅 작업의 조회 테이블을 작성하고 `controller` 및 `action` 값을 매칭하여 URL 생성에 사용할 경로 템플릿을 선택합니다. 위의 예제에서는 `custom/url/to/destination`이 생성됩니다.

### <a name="generating-urls-by-action-name"></a>작업 이름으로 URL 생성

`Url.Action` (`IUrlHelper` . `Action`) 및 모든 관련 오버로드는 개발자가 컨트롤러 이름과 작업 이름을 지정하여 연결 대상을 지정하려 한다는 생각을 바탕으로 합니다.

> [!NOTE]
> `Url.Action`을 사용하면 `controller` 및 `action`의 현재 경로 값이 자동으로 지정됩니다. `controller` 및 `action`의 값은 모두 *앰비언트 값* **및** *값* 의 일부입니다. `Url.Action` 메서드는 항상 `action` 및 `controller`의 현재 값을 사용하며 현재 작업에 라우팅하는 URL 경로를 생성합니다.

라우팅은 앰비언트 값의 값을 사용하여 개발자가 URL을 생성할 때 제공하지 않은 정보를 채웁니다. `{a}/{b}/{c}/{d}` 같은 경로와 `{ a = Alice, b = Bob, c = Carol, d = David }` 앰비언트 값을 사용하면 모든 경로 매개 변수가 값을 갖기 때문에 추가 값이 없어도 라우팅에서 URL을 생성하기에 충분한 정보가 제공됩니다. `{ d = Donovan }` 값을 추가하면 `{ d = David }` 값이 무시되고 `Alice/Bob/Carol/Donovan` URL 경로가 생성됩니다.

> [!WARNING]
> URL 경로는 계층적입니다. 위의 예에서 `{ c = Cheryl }` 값을 추가하면 두 `{ c = Carol, d = David }` 값이 모두 무시됩니다. 이 경우 `d`의 값이 없기 때문에 URL 생성이 실패합니다. 원하는 `c` 및 `d` 값을 지정해야 합니다.  기본 경로(`{controller}/{action}/{id?}`)를 사용해도 이 문제가 발생할 가능성은 있지만, `Url.Action`이 항상 `controller` 및 `action` 값을 명시적으로 지정하기 때문에 실제로 이 동작이 발생하는 경우는 극히 드뭅니다.

`Url.Action` 의 긴 오버로드에서는 `controller` 및 `action` 이외의 경로 매개 변수의 값을 제공하기 위한 추가 *경로 값* 개체가 사용됩니다. `Url.Action("Buy", "Products", new { id = 17 })`처럼 `id`와 함께 사용되는 것을 흔히 볼 수 있습니다. 관례상 *경로 값* 개체는 대부분 무명 형식의 개체지만, `IDictionary<>` 또는 *오래된 일반 .NET 개체* 일 수도 있습니다. 경로 매개 변수와 일치하지 않는 추가 경로 값들은 쿼리 문자열에 포함됩니다.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> 절대 URL을 만들려면 `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`을 받는 오버로드를 사용합니다.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>경로로 URL 생성

위의 코드에서는 컨트롤러 및 작업 이름을 전달하여 URL을 생성하는 방법을 보여줍니다. `IUrlHelper`는 `Url.RouteUrl` 메서드 모음도 제공합니다. 이 메서드는 `Url.Action`과 비슷하지만, `action` 및 `controller`의 현재 값을 경로 값에 복사하지 않습니다. 가장 일반적인 사용법은 URL을 생성할 경로를 지정하기 위한 경로 이름을 지정하는 것으로, 일반적으로 컨트롤러 또는 작업 이름을 지정하지 *않습니다*.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>HTML에서 URL 생성

`IHtmlHelper`는 각각 `<form>` 및 `<a>` 요소를 생성하는 `HtmlHelper` 메서드인 `Html.BeginForm` 및 `Html.ActionLink`를 제공합니다. 이러한 메서드는 `Url.Action` 메서드를 사용하여 URL을 생성하며 비슷한 인수를 받습니다. `HtmlHelper`에 대한 `Url.RouteUrl` 보조 도구는 `Html.BeginRouteForm` 및 `Html.RouteLink`이며 서로 기능이 비슷합니다.

TagHelper는 `form` TagHelper 및 `<a>` TagHelper를 통해 URL을 생성합니다. 둘 다 구현에 `IUrlHelper`를 사용합니다. 자세한 내용은 [양식 작업](../views/working-with-forms.md)을 참조하세요.

보기 내에서 `IUrlHelper`는 `Url` 속성을 통해서 위에서 다루지 않은 임시 URL을 생성하기 위해 사용할 수 있습니다.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>작업 결과에 URL 생성

위의 예제에서는 컨트롤러에서 `IUrlHelper`를 사용하는 방법을 보여주었으며, 컨트롤러에서 가장 일반적인 사용법은 URL을 작업 결과의 일부로 생성하는 것입니다.

`ControllerBase` 및 `Controller` 기본 클래스는 다른 작업을 참조하는 작업 결과에 대한 편의 메서드를 제공합니다. 한 가지 일반적인 사용법은 사용자 입력을 수락한 후 리디렉션하는 것입니다.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

작업 결과 팩터리 메서드는 `IUrlHelper`의 메서드와 비슷한 패턴을 따릅니다.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>전용 규칙 기반 경로의 특별한 사례

규칙 기반 라우팅은 *전용 규칙 기반 경로* 라고 하는 특별한 경로 정의를 사용할 수 있습니다. 아래 예제에서 `blog`라는 이름의 경로는 전용 규칙 기반 경로입니다.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

이러한 경로 정의를 사용하면 `Url.Action("Index", "Home")`은 `default` 경로를 사용하여 URL 경로 `/`를 생성합니다. 그런데 그 이유는 무엇일까요? `{ controller = Home, action = Index }` 경로 값이면 `blog`를 사용하여 URL을 생성하기에 충분하며, 그 결과는 `/blog?action=Index&controller=Home`가 될 것이라고 추측할 수도 있습니다.

전용 규칙 기반 경로는 URL 생성과 관련하여 경로의 "지나친 욕심"을 방지하는 해당 경로 매개 변수가 없는 기본 값의 특별한 동작을 사용합니다. 이 예제에서 기본값은 `{ controller = Blog, action = Article }`이고, `controller` 및 `action`이 경로 매개 변수로 표시되지 않습니다. 라우팅이 URL 생성을 수행할 때 입력한 값이 기본값과 일치해야 합니다. `{ controller = Home, action = Index }` 값이 `{ controller = Blog, action = Article }`과 일치하지 않기 때문에 `blog`를 사용한 URL 생성은 실패합니다. 그 후 라우팅이 `default`로 대체되고, 이것은 성공합니다.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>영역

[영역](areas.md)은 관련 기능을 별도의 라우팅-네임스페이스(컨트롤러 작업용) 및 폴더 구조(보기용)로 그룹화하는 데 사용되는 MVC 기능입니다. 영역을 사용하면 컨트롤러의 *영역* 이 서로 다른 한, 응용 프로그램 하나에서 이름이 같은 컨트롤러를 여러 개 사용할 수 있습니다. 영역을 사용하면 또 다른 경로 매개 변수 `area`를 `controller` 및 `action`에 추가하여 라우팅을 위한 계층 구조를 생성합니다. 이 섹션에서는 라우팅이 영역과 상호 작용하는 방법을 설명합니다. 보기에 영역을 사용하는 자세한 방법은 [영역](areas.md)을 참조하세요.

다음 예제는 기본 규칙 기반 경로 및 `Blog`라는 이름의 영역에 대한 *영역 경로* 를 사용하도록 MVC를 구성합니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

`/Manage/Users/AddUser` 같은 URL 경로를 매칭할 때, 첫 번째 경로는 `{ area = Blog, controller = Users, action = AddUser }` 경로 값을 생성합니다. `area` 경로 값은 `area`의 기본값을 사용하여 생성되며, 사실 `MapAreaRoute`를 통해 생성되는 경로는 다음과 같습니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`는 제공된 영역 이름(여기에서는 `Blog`)을 사용하는 `area`에 대해 기본값 및 제약 조건을 모두 사용하여 경로를 생성합니다. 기본값은 경로가 항상 `{ area = Blog, ... }`를 생성함을 보장하고, 제약 조건은 URL 생성을 위해 `{ area = Blog, ... }` 값을 필요로 합니다.

> [!TIP]
> 규칙 기반 라우팅은 순서에 영향을 받습니다. 일반적으로 영역이 있는 경로는 영역이 없는 경로에 비해 구체적이기 때문에 경로 테이블의 앞부분에 배치되어야 합니다.

위의 예제를 사용하면 경로 값이 다음 작업과 매칭됩니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute`는 컨트롤러가 영역의 일부임을 나타내며, 이때 이 컨트롤러가 `Blog` 영역에 있다고 표현합니다. `[Area]` 특성이 없는 컨트롤러는 어떠한 영역의 구성원도 아니며, 라우팅에서 `area` 경로 값을 제공해도 매칭되지 **않습니다**. 다음 예제에서는 나열된 첫 번째 컨트롤러만 `{ area = Blog, controller = Users, action = AddUser }` 경로 값과 매칭됩니다.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> 각 컨트롤러의 네임스페이스는 완전성을 위해서 사용되었습니다. 네임스페이스가 없으면 컨트롤러에서 이름 충돌이 발생하고 컴파일러 오류가 생성됩니다. 클래스 네임스페이스는 MVC의 라우팅에 영향을 주지 않습니다.

처음 두 컨트롤러는 영역의 구성원이며, `area` 경로 값으로 해당 영역 이름을 제공하는 경우에만 매칭됩니다. 세 번째 컨트롤러는 어떤 영역의 구성원도 아니며, 라우팅에서 `area`에 대한 값을 제공하지 않을 때에만 매칭됩니다.

> [!NOTE]
> 매칭 측면에서 *값 없음*, 즉 `area` 값이 없는 것은 `area`의 값이 null 또는 빈 문자열인 경우와 동일합니다.

영역 내에서 작업을 실행하면 `area`의 경로 값이 라우팅에서 URL을 생성하기 위해 사용되는 *앰비언트 값* 으로 제공됩니다. 즉, 기본적으로 영역은 다음 예제에서 볼 수 있듯이 URL 생성을 위한 *스티커처럼* 동작합니다.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>IActionConstraint 이해

> [!NOTE]
> 이 섹션에서는 프레임워크의 내부 구조 및 MVC가 실행할 작업을 선택하는 원리를 자세히 알아봅니다. 일반적인 응용 프로그램에는 사용자 지정 `IActionConstraint`가 필요 없습니다.

이 인터페이스에 익숙하지 않은 분들도 이미 `IActionConstraint`를 사용해 보셨을 것입니다. `[HttpGet]` 특성 그리고 그와 비슷한 `[Http-VERB]` 특성들은 `IActionConstraint`를 구현하여 작업 메서드 실행을 제한합니다.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

기본 규칙 기반 경로를 사용한다고 가정할 때, URL 경로 `/Products/Edit`는 여기에 보이는 두 작업 **모두** 와 매칭하는 `{ controller = Products, action = Edit }` 값을 생성합니다. `IActionConstraint` 용어에서는 두 작업이 경로 데이터와 매칭되므로 두 작업이 후보로 간주된다고 말할 수 있습니다.

`HttpGetAttribute`가 실행되면 *Edit()* 는 *GET* 에 대해서만 일치하고 다른 HTTP 동사에 대해서는 일치하지 않습니다. `Edit(...)` 작업은 정의된 제약 조건이 없으므로 모든 HTTP 동사와 매칭됩니다. 따라서 `POST`를 고려해본다면 `Edit(...)`만 일치합니다. 하지만 `GET`의 경우에는 여전히 두 작업 모두 매칭될 수 있습니다. 그러나`IActionConstraint`가 적용된 작업이 적용되지 않은 작업보다 항상 *더 적합한* 것으로 간주됩니다. 따라서 `Edit()`에는 `[HttpGet]`가 있으므로 보다 구체적인 것으로 간주되며, 두 작업이 모두 매칭될 수 있는 경우에도 이 작업이 선택됩니다.

개념적으로 `IActionConstraint`는 *오버로딩* 의 한 가지 형태이지만, 같은 이름의 메서드를 오버로딩하는 대신, 동일한 URL을 매칭하는 작업 간에 오버로딩합니다. 특성 라우팅에서도 `IActionConstraint`를 사용하며, 다른 두 컨트롤러의 작업이 모두 후보로 간주될 수 있습니다.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>IActionConstraint 구현

`IActionConstraint`를 구현하는 가장 간단한 방법은 `System.Attribute`에서 파생된 클래스를 만들어서 작업 및 컨트롤러에 배치하는 것입니다. MVC는 특성으로 적용된 `IActionConstraint`를 자동으로 검색합니다. 애플리케이션 모델을 사용하여 제약 조건을 적용할 수 있으며, 제약 조건이 적용되는 방식을 개발자가 메타프로그래밍할 수 있는 가장 유연한 접근 방식일 것입니다.

다음 예에서는 제약 조건이 경로 데이터의 *국가 코드* 를 기반으로 하는 작업을 선택 합니다. [GitHub의 전체 예제](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

개발자는 `Accept` 메서드를 구현하고 제약 조건이 실행되는 '순서'를 지정해야 합니다. 이 예제에서 `Accept` 메서드는 `country` 경로 값이 일치하면 작업이 일치함을 나타내기 위해 `true`를 반환합니다. 비-특성 작업으로 대체할 수 있다는 점에서 `RouteValueAttribute`와는 다릅니다. 이 예제는 `en-US` 작업을 정의하면 `fr-FR` 같은 국가 코드는 `[CountrySpecific(...)]`이 적용되지 않은 보다 일반적인 컨트롤러로 대체되는 것을 보여줍니다.

`Order` 속성은 제약 조건이 소속되는 *단계* 를 결정합니다. 작업 제약 조건은 `Order`에 따라 그룹으로 실행됩니다. 예를 들어 프레임워크에서 제공하는 모든 HTTP 메서드 특성은 같은 단계에서 실행될 수 있도록 동일한 `Order` 값을 사용합니다. 원하는 정책을 구현하는 데 필요한 만큼 단계를 포함할 수 있습니다.

> [!TIP]
> `Order`에 대한 값을 결정하려면 HTTP 메서드보다 제약 조건이 먼저 적용되어야 하는지 생각해야 합니다. 숫자가 낮을수록 먼저 실행됩니다.

::: moniker-end
