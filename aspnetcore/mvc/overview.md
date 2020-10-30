---
title: ASP.NET Core MVC 개요
author: ardalis
description: 모델-보기-컨트롤러 디자인 패턴을 사용하여 웹앱 및 API를 빌드할 수 있는 풍부한 프레임워크인 ASP.NET Core MVC에 대해 알아봅니다.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/overview
ms.openlocfilehash: c55421efa1fd559f462549bfaceb794d4a9c35b8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059314"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="51fb2-103">ASP.NET Core MVC 개요</span><span class="sxs-lookup"><span data-stu-id="51fb2-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="51fb2-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="51fb2-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="51fb2-105">ASP.NET Core MVC는 모델-보기-컨트롤러 디자인 패턴을 사용하여 웹앱 및 API를 만들 수 있는 풍부한 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="51fb2-106">MVC 패턴이란?</span><span class="sxs-lookup"><span data-stu-id="51fb2-106">What is the MVC pattern?</span></span>

<span data-ttu-id="51fb2-107">MVC(모델-뷰-컨트롤러) 아키텍처 패턴은 애플리케이션을 모델, 보기, 컨트롤러라는 세 가지 주요 구성 요소로 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="51fb2-108">이 패턴은 [문제를 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="51fb2-109">이 패턴을 사용하면 사용자 요청은 모델 작업을 담당하는 컨트롤러에 라우팅되어 사용자 작업을 수행하고/수행하거나 쿼리 결과를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="51fb2-110">컨트롤러는 사용자에게 표시할 보기를 선택하고, 보기에 필요한 모델 데이터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="51fb2-111">다음 다이어그램은 세 가지 주요 구성 요소 및 다른 구성 요소를 참조하는 구성 요소를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![MVC 패턴](overview/_static/mvc.png)

<span data-ttu-id="51fb2-113">이와 같은 명확한 책임의 규정은 복잡성의 측면에서 애플리케이션의 크기를 조정하는 데 도움을 주는데, 작업이 하나만 있는 항목(모델, 보기 또는 컨트롤러)을 좀 더 쉽게 코딩, 디버그 및 테스트할 수 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="51fb2-114">이러한 세 영역 중 두 개 이상에 종속된 코드는 업데이트, 테스트 및 디버깅이 더 어렵습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="51fb2-115">예를 들어 사용자 인터페이스 논리는 비즈니스 논리보다 자주 변하는 경향이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="51fb2-116">프레젠테이션 코드와 비즈니스 논리가 단일 개체에 결합되면 사용자 인터페이스가 변경될 때마다 비즈니스 논리를 포함하고 있는 개체를 수정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="51fb2-117">이 때문에 자주 오류가 발생하며 모든 사소한 사용자 인터페이스 변경 시마다 비즈니스 논리를 다시 테스트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="51fb2-118">보기와 컨트롤러는 모두 모델에 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="51fb2-119">그러나 모델은 보기 및 컨트롤러에 의존하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="51fb2-120">이것이 바로 분리의 주요 이점 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="51fb2-121">이와 같은 분리 덕분에 시각적 표시에 관계없이 모델을 만들고 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="51fb2-122">모델의 책임</span><span class="sxs-lookup"><span data-stu-id="51fb2-122">Model Responsibilities</span></span>

<span data-ttu-id="51fb2-123">MVC 애플리케이션의 모델은 애플리케이션 및 비즈니스 논리 또는 이를 통해 수행해야 하는 작업의 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="51fb2-124">비즈니스 논리는 애플리케이션의 상태를 유지하기 위한 구현 논리와 함께 모델에 캡슐화해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="51fb2-125">강력한 형식의 보기는 일반적으로 해당 보기에 표시할 데이터를 포함하도록 디자인된 ViewModel 형식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="51fb2-126">컨트롤러는 모델에서 이러한 ViewModel 인스턴스를 만들고 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="51fb2-127">보기의 책임</span><span class="sxs-lookup"><span data-stu-id="51fb2-127">View Responsibilities</span></span>

<span data-ttu-id="51fb2-128">보기는 사용자 인터페이스를 통해 콘텐츠를 제공할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="51fb2-129">[ :::no-loc(Razor)::: 뷰 엔진](#razor-view-engine) 을 사용 하 여 .NET 코드를 HTML 태그에 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-129">They use the [:::no-loc(Razor)::: view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="51fb2-130">보기 내부의 논리를 최소화해야 하며, 보기의 모든 논리는 콘텐츠 제공과 관련되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="51fb2-131">복잡한 모델의 데이터를 표시하기 위해 보기에서 다량의 논리를 수행해야 하는 경우 [보기 구성 요소](views/view-components.md), ViewModel 또는 보기 템플릿을 사용하여 보기를 간소화하는 방안을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="51fb2-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="51fb2-132">컨트롤러의 책임</span><span class="sxs-lookup"><span data-stu-id="51fb2-132">Controller Responsibilities</span></span>

<span data-ttu-id="51fb2-133">컨트롤러는 사용자 상호 작용을 처리하고, 모델을 작업하고, 궁극적으로 렌더링할 보기를 선택하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="51fb2-134">MVC 애플리케이션에서 보기는 정보만 표시합니다. 컨트롤러가 사용자 입력 및 상호 작용을 처리하고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="51fb2-135">MVC 패턴에서 컨트롤러는 초기 진입점으로, 작업할 모델과 렌더링할 보기를 선택할 책임이 있습니다(그 이름처럼 앱이 지정된 요청에 응답하는 방식을 제어).</span><span class="sxs-lookup"><span data-stu-id="51fb2-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="51fb2-136">컨트롤러에 너무 많은 책임을 부여하여 지나치게 복잡하게 만들면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="51fb2-137">컨트롤러 논리가 너무 복잡해지지 않도록 컨트롤러에서 도메인 모델로 비즈니스 논리를 푸시합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="51fb2-138">컨트롤러 작업이 같은 종류의 작업을 자주 수행하는 것을 발견하는 경우 이러한 공통 작업을 [필터](#filters)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="51fb2-139">ASP.NET Core MVC란?</span><span class="sxs-lookup"><span data-stu-id="51fb2-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="51fb2-140">ASP.NET Core MVC 프레임워크는 ASP.NET Core에 사용하도록 최적화된 가볍고 테스트가 용이한 프레젠테이션 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="51fb2-141">ASP.NET Core MVC는 문제를 깔끔하게 분리하는 동적 웹 사이트를 빌드하는 강력한 패턴 기반 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="51fb2-142">태그를 완벽하게 제어할 수 있고, TDD에 친숙한 개발을 지원하고, 최신 웹 표준을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="51fb2-143">기능</span><span class="sxs-lookup"><span data-stu-id="51fb2-143">Features</span></span>

<span data-ttu-id="51fb2-144">ASP.NET Core MVC는 다음과 같은 기능을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="51fb2-145">라우팅</span><span class="sxs-lookup"><span data-stu-id="51fb2-145">Routing</span></span>](#routing)
* [<span data-ttu-id="51fb2-146">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="51fb2-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="51fb2-147">모델 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="51fb2-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="51fb2-148">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="51fb2-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="51fb2-149">필터</span><span class="sxs-lookup"><span data-stu-id="51fb2-149">Filters</span></span>](#filters)
* [<span data-ttu-id="51fb2-150">Areas</span><span class="sxs-lookup"><span data-stu-id="51fb2-150">Areas</span></span>](#areas)
* [<span data-ttu-id="51fb2-151">Web API</span><span class="sxs-lookup"><span data-stu-id="51fb2-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="51fb2-152">테스트 가능성</span><span class="sxs-lookup"><span data-stu-id="51fb2-152">Testability</span></span>](#testability)
* [<span data-ttu-id="51fb2-153">:::no-loc(Razor)::: 뷰 엔진</span><span class="sxs-lookup"><span data-stu-id="51fb2-153">:::no-loc(Razor)::: view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="51fb2-154">강력한 형식의 뷰</span><span class="sxs-lookup"><span data-stu-id="51fb2-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="51fb2-155">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="51fb2-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="51fb2-156">구성 요소 보기</span><span class="sxs-lookup"><span data-stu-id="51fb2-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="51fb2-157">라우팅</span><span class="sxs-lookup"><span data-stu-id="51fb2-157">Routing</span></span>

<span data-ttu-id="51fb2-158">ASP.NET Core MVC는 [ASP.NET Core 라우팅](../fundamentals/routing.md)을 기반으로 하며, 알기 쉽고 검색 가능한 URL이 있는 애플리케이션을 빌드할 수 있는 강력한 URL 매핑 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="51fb2-159">웹 서버에 있는 파일의 구성 방식에 관계없이 SEO(검색 엔진 최적화) 및 링크 생성에 적합한 애플리케이션 URL 이름 지정 패턴을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="51fb2-160">경로 값 제약 조건, 기본값 및 선택적 값을 지원하는 편리한 경로 템플릿 구문을 사용하여 경로를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="51fb2-161">*규칙 기반 라우팅* 을 사용하면 응용 프로그램이 허용하는 URL 형식과 이러한 각 형식이 특정 컨트롤러의 특정 작업 메서드에 매핑되는 방식을 전체적으로 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="51fb2-162">들어오는 요청이 수신되면 라우팅 엔진이 URL을 구문 분석하여 정의된 URL 형식 중 하나와 매칭한 후 관련 컨트롤러의 작업 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="51fb2-163">*특성 라우팅* 을 사용하면 응용 프로그램의 경로를 정의하는 특성으로 컨트롤러와 작업을 데코레이팅하여 라우팅 정보를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="51fb2-164">즉, 경로 정의는 연결된 컨트롤러 및 작업 옆에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

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

### <a name="model-binding"></a><span data-ttu-id="51fb2-165">모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="51fb2-165">Model binding</span></span>

<span data-ttu-id="51fb2-166">ASP.NET Core MVC [모델 바인딩](models/model-binding.md)은 클라이언트 요청 데이터(양식 값, 경로 데이터, 쿼리 문자열 매개 변수, HTTP 헤더)를 컨트롤러에서 처리할 수 있는 개체로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="51fb2-167">그 결과, 컨트롤러 논리는 들어오는 요청 데이터를 파악하는 작업을 할 필요가 없습니다. 데이터를 작업 메서드에 대한 매개 변수로 갖고 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a><span data-ttu-id="51fb2-168">모델 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="51fb2-168">Model validation</span></span>

<span data-ttu-id="51fb2-169">ASP.NET Core MVC는 모델 개체를 데이터 주석 유효성 검사 특성으로 데코레이팅하여 [유효성 검사](models/validation.md)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="51fb2-170">서버에 값이 게시되기 전에 클라이언트 쪽에서 유효성 검사 특성이 확인될 뿐만 아니라 서버에서도 컨트롤러 작업이 호출되기 전에 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

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

<span data-ttu-id="51fb2-171">컨트롤러 작업:</span><span class="sxs-lookup"><span data-stu-id="51fb2-171">A controller action:</span></span>

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

<span data-ttu-id="51fb2-172">프레임워크는 클라이언트 및 서버의 요청 데이터 유효성 검사를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="51fb2-173">모델 형식에 지정된 유효성 검사 논리는 렌더링된 보기에 비간섭 주석으로 추가되고 [jQuery 유효성 검사](https://jqueryvalidation.org/)를 통해 브라우저에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="51fb2-174">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="51fb2-174">Dependency injection</span></span>

<span data-ttu-id="51fb2-175">ASP.NET Core는 기본적으로 [DI(종속성 주입 )](../fundamentals/dependency-injection.md)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="51fb2-176">ASP.NET Core MVC에서 [컨트롤러](controllers/dependency-injection.md)는 생성자를 통해서 필요한 서비스를 요청하여 [명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따르도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="51fb2-177">또한 앱에서 `@inject` 지시문을 사용하여 [보기 파일에서 종속성 주입](views/dependency-injection.md)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

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

### <a name="filters"></a><span data-ttu-id="51fb2-178">필터</span><span class="sxs-lookup"><span data-stu-id="51fb2-178">Filters</span></span>

<span data-ttu-id="51fb2-179">[필터](controllers/filters.md)는 개발자가 예외 처리 또는 권한 부여 같은 교차 편집 문제를 캡슐화하도록 도와줍니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="51fb2-180">필터는 작업 메서드에 대한 사용자 지정 전처리 및 후처리 논리 실행을 지원하며, 지정된 요청에 대한 실행 파이프라인 내부의 특정 지점에서 실행되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="51fb2-181">필터는 컨트롤러 또는 작업에 특성으로 적용할 수 있습니다(또는 전역적으로 실행할 수도 있습니다).</span><span class="sxs-lookup"><span data-stu-id="51fb2-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="51fb2-182">여러 필터(예: `Authorize`)가 프레임워크에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="51fb2-183">`[Authorize]`는 MVC 권한 부여 필터를 만드는 데 사용되는 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="51fb2-184">Areas</span><span class="sxs-lookup"><span data-stu-id="51fb2-184">Areas</span></span>

<span data-ttu-id="51fb2-185">[영역](controllers/areas.md)은 대규모 ASP.NET Core MVC 웹앱을 더 작은 기능 그룹으로 분할하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="51fb2-186">영역은 애플리케이션 내부의 MVC 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="51fb2-187">MVC 프로젝트에서 모델, 컨트롤러, 보기와 같은 논리적 구성 요소는 서로 다른 폴더에 보관되며 MVC는 명명 규칙을 사용하여 이러한 구성 요소 간의 관계를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="51fb2-188">대형 앱의 경우 앱을 별도의 고급 기능 영역으로 나누는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="51fb2-189">예를 들어 체크 아웃, 대금 청구, 검색 등의 여러 사업부를 포함 하는 전자 상거래 앱입니다. 이러한 각 단위에는 고유한 논리적 구성 요소 뷰, 컨트롤러 및 모델이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="51fb2-190">Web API</span><span class="sxs-lookup"><span data-stu-id="51fb2-190">Web APIs</span></span>

<span data-ttu-id="51fb2-191">ASP.NET Core MVC는 웹 사이트를 구축할 수 있는 훌륭한 플랫폼일 뿐 아니라 웹 API 빌드를 잘 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="51fb2-192">브라우저 및 모바일 디바이스를 비롯한 광범위한 클라이언트에 연결하는 서비스를 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="51fb2-193">이 프레임워크는 JSON 또는 XML로 [데이터 형식을 지정](xref:web-api/advanced/formatting)하는 기본 제공 지원을 통해 HTTP 콘텐츠 협상을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="51fb2-194">[사용자 지정 포맷터](xref:web-api/advanced/custom-formatters)를 작성하여 사용자 고유의 형식에 대한 지원을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="51fb2-195">링크 생성을 사용하여 하이퍼미디어를 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="51fb2-196">여러 웹 응용 프로그램에서 Web API를 공유할 수 있도록 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 간단하게 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-196">Easily enable support for [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="51fb2-197">테스트 가능성</span><span class="sxs-lookup"><span data-stu-id="51fb2-197">Testability</span></span>

<span data-ttu-id="51fb2-198">이 프레임워크의 인터페이스 및 종속성 주입 사용은 단위 테스트에 적합하며, [통합 테스트](xref:test/integration-tests)를 쉽고 빠르게 수행할 수 있는 기능(예: Entity Framework용 TestHost 및 InMemory 공급자)을 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="51fb2-199">[컨트롤러 논리를 테스트하는 방법](controllers/testing.md)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="51fb2-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="no-locrazor-view-engine"></a><span data-ttu-id="51fb2-200">:::no-loc(Razor)::: 뷰 엔진</span><span class="sxs-lookup"><span data-stu-id="51fb2-200">:::no-loc(Razor)::: view engine</span></span>

<span data-ttu-id="51fb2-201">[MVC 뷰 ASP.NET Core](views/overview.md) 뷰 [ :::no-loc(Razor)::: 엔진](views/razor.md) 을 사용 하 여 뷰를 렌더링 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-201">[ASP.NET Core MVC views](views/overview.md) use the [:::no-loc(Razor)::: view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="51fb2-202">:::no-loc(Razor)::: 는 포함 된 c # 코드를 사용 하 여 뷰를 정의 하기 위한 간결 하 고, 표현 하기 위한 유체 템플릿 태그 언어입니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-202">:::no-loc(Razor)::: is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="51fb2-203">:::no-loc(Razor)::: 는 서버에서 웹 콘텐츠를 동적으로 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-203">:::no-loc(Razor)::: is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="51fb2-204">서버 코드를 클라이언트 쪽 콘텐츠 및 코드와 깔끔하게 혼합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-204">You can cleanly mix server code with client side content and code.</span></span>

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

<span data-ttu-id="51fb2-205">:::no-loc(Razor):::뷰 엔진을 사용 하 여 [레이아웃](views/layout.md), [부분 뷰](views/partial.md) 및 대체 가능 섹션을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-205">Using the :::no-loc(Razor)::: view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="51fb2-206">강력한 형식의 보기</span><span class="sxs-lookup"><span data-stu-id="51fb2-206">Strongly typed views</span></span>

<span data-ttu-id="51fb2-207">:::no-loc(Razor)::: MVC의 뷰는 모델을 기반으로 강력 하 게 형식화 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-207">:::no-loc(Razor)::: views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="51fb2-208">컨트롤러는 강력한 형식의 모델을 보기에 전달하여 보기에서 형식을 검사하고 IntelliSense를 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="51fb2-209">예를 들어 다음 보기는 `IEnumerable<Product>` 형식의 모델을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="51fb2-210">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="51fb2-210">Tag Helpers</span></span>

<span data-ttu-id="51fb2-211">[태그 도우미](views/tag-helpers/intro.md) 를 사용 하면 서버 쪽 코드에서 파일에 HTML 요소를 만들고 렌더링 하는 데 참여할 수 :::no-loc(Razor)::: 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="51fb2-212">태그 도우미를 사용하여 사용자 지정 태그를 정의하거나(예: `<environment>`) 기존 태그의 동작을 수정할 수 있습니다(예: `<label>`).</span><span class="sxs-lookup"><span data-stu-id="51fb2-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="51fb2-213">태그 도우미는 요소 이름 및 해당 특성에 따라 특정 요소에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="51fb2-214">서버 쪽 렌더링의 이점을 제공하면서도 HTML 편집 환경을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="51fb2-215">양식 작성, 링크, 자산 로드 등의 일반적인 작업을 위한 여러 가지 기본 제공 태그 도우미가 있으며, 공용 GitHub 리포지토리 및 NuGet 패키지로도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="51fb2-216">태그 도우미는 C#에서 작성되며 요소 이름, 특성 이름 또는 부모 태그 기반의 HTML 요소를 대상으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="51fb2-217">예를 들어 기본 제공 LinkTagHelper를 사용하여 `AccountsController`의 `Login` 작업에 대한 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="51fb2-218">`EnvironmentTagHelper`를 사용하여 개발, 스테이징 또는 프로덕션 같은 런타임 환경에 따라 다른 스크립트(예: 원본 또는 최소화된)를 보기에 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

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

<span data-ttu-id="51fb2-219">태그 도우미는 html 기반의 개발 환경과 HTML 및 태그를 만들기 위한 풍부한 IntelliSense 환경을 제공 :::no-loc(Razor)::: 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and :::no-loc(Razor)::: markup.</span></span> <span data-ttu-id="51fb2-220">대부분의 기본 제공 태그 도우미는 기존 HTML 요소를 대상으로 하며 요소에 대한 서버 쪽 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="51fb2-221">보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="51fb2-221">View Components</span></span>

<span data-ttu-id="51fb2-222">[보기 구성 요소](views/view-components.md)를 통해 렌더링 논리를 패키지하여 응용 프로그램에서 다시 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="51fb2-223">보기 구성 요소는 [부분 보기](views/partial.md)와 비슷하지만, 논리가 연결되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="51fb2-224">호환성 버전</span><span class="sxs-lookup"><span data-stu-id="51fb2-224">Compatibility version</span></span>

<span data-ttu-id="51fb2-225"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드를 사용하면 ASP.NET Core MVC 2.1 이상에서 도입된 주요 동작 변경 내용을 앱이 옵트인(opt-in) 또는 옵트아웃(opt-out)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="51fb2-226">자세한 내용은 <xref:mvc/compatibility-version>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="51fb2-226">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51fb2-227">추가 자료</span><span class="sxs-lookup"><span data-stu-id="51fb2-227">Additional resources</span></span>

* <span data-ttu-id="51fb2-228">[AspNetCore-ASP.NET CORE mvc에 대 한 흐름 테스트 라이브러리](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): 강력한 형식의 단위 테스트 라이브러리로, mvc 및 web API 앱 테스트를 위한 흐름 인터페이스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="51fb2-228">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="51fb2-229">( *Microsoft에서 유지 관리하거나 지원하지 않습니다.* )</span><span class="sxs-lookup"><span data-stu-id="51fb2-229">( *Not maintained or supported by Microsoft.* )</span></span>
* <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>
