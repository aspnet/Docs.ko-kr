---
title: ASP.NET Core의 응용 프로그램 부분과 컨트롤러, 뷰, 페이지 등을 공유 합니다. Razor
author: rick-anderson
description: RazorASP.NET Core의 응용 프로그램 부분을 사용 하 여 컨트롤러, 뷰, 페이지 등 공유
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061186"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a>Razor응용 프로그램 파트를 사용 하 여 컨트롤러, 뷰, 페이지 등 공유

::: moniker range=">= aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))

*애플리케이션 파트* 는 앱의 리소스에 대한 추상화입니다. 응용 프로그램 파트를 사용 하 여 컨트롤러 검색, 구성 요소 보기, 태그 도우미, Razor 페이지, razor 컴파일 소스 등을 ASP.NET Core 수 있습니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>는 애플리케이션 파트입니다. `AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.

[기능 공급자](#fp)는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다. 애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다. 예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다. 응용 프로그램 파트를 사용 하 여 여러 앱에서 컨트롤러, 뷰, Razor 페이지, razor 컴파일 소스, 태그 도우미 등을 포함 하는 어셈블리 (DLL)를 공유할 수 있습니다. 여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.

ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.

## <a name="load-aspnet-core-features"></a>ASP.NET Core 기능 로드

<xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 및 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다. `ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다. `SharedController`는 앱의 프로젝트에 없습니다. [WebAppParts 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) 샘플 다운로드를 참조하세요.

### <a name="include-views"></a>보기 포함

[ Razor 클래스 라이브러리](xref:razor-pages/ui-class) 를 사용 하 여 어셈블리에 뷰를 포함 합니다.

### <a name="prevent-loading-resources"></a>리소스 로드 방지

애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지* 할 수 있습니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다. `ApplicationParts` 컬렉션에 있는 항목의 순서는 중요하지 않습니다. 컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다. 예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다. `ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.

`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.

* 앱의 어셈블리 및 종속 어셈블리.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>기능 공급자

애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다. 다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속됩니다. 여기서 `T`는 기능의 형식입니다. 기능 공급자는 이전에 나열된 모든 기능 형식에 대해 구현할 수 있습니다. `ApplicationPartManager.FeatureProviders`의 기능 공급자 순서는 런타임 동작에 영향을 줄 수 있습니다. 나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.

### <a name="display-available-features"></a>사용 가능 기능 표시

앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>애플리케이션 파트에서 검색

HTTP 404 오류는 애플리케이션 파트를 사용하여 개발할 때 일반적이지는 않습니다. 이러한 오류는 일반적으로 애플리케이션 파트를 검색하는 방법에 대한 필수 요구 사항이 없기 때문에 발생합니다. 앱에서 HTTP 404 오류를 반환하는 경우 다음 요구 사항이 충족되었는지 확인합니다.

* `applicationName` 설정은 검색에 사용되는 루트 어셈블리로 설정해야 합니다. 검색에 사용되는 루트 어셈블리는 일반적으로 진입점 어셈블리입니다.
* 루트 어셈블리에는 검색에 사용되는 파트에 대한 참조가 있어야 합니다. 참조는 직접적 또는 전이적일 수 있습니다.
* 루트 어셈블리는 웹 SDK를 참조해야 합니다. 프레임워크에는 검색에 사용되는 루트 어셈블리에 특성을 스탬핑하는 논리가 있습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([다운로드 방법](xref:index#how-to-download-a-sample))

*애플리케이션 파트* 는 앱의 리소스에 대한 추상화입니다. 응용 프로그램 파트를 사용 하 여 컨트롤러 검색, 구성 요소 보기, 태그 도우미, Razor 페이지, razor 컴파일 소스 등을 ASP.NET Core 수 있습니다. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart)는 애플리케이션 파트입니다. `AssemblyPart`는 어셈블리 참조를 캡슐화하고 형식 및 컴파일 참조를 공개합니다.

*기능 공급자* 는 애플리케이션 파트를 사용하여 ASP.NET Core 앱의 기능을 구성합니다. 애플리케이션 파트에 대한 주요 사용 사례는 어셈블리에서 ASP.NET Core 기능을 검색(또는 로드를 방지)하도록 앱을 구성하는 것입니다. 예를 들어 여러 앱 간에 공통적인 기능을 공유하는 것이 좋습니다. 응용 프로그램 파트를 사용 하 여 여러 앱에서 컨트롤러, 뷰, Razor 페이지, razor 컴파일 소스, 태그 도우미 등을 포함 하는 어셈블리 (DLL)를 공유할 수 있습니다. 여러 프로젝트에서 코드를 복제하려면 어셈블리를 공유하는 것이 좋습니다.

ASP.NET Core 앱은 <xref:System.Web.WebPages.ApplicationPart>로부터 기능을 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 클래스는 어셈블리에서 지원하는 애플리케이션 파트를 나타냅니다.

## <a name="load-aspnet-core-features"></a>ASP.NET Core 기능 로드

`ApplicationPart` 및 `AssemblyPart` 클래스를 사용하여 ASP.NET Core 기능(컨트롤러, 보기 구성 요소 등)을 검색 및 로드합니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager>는 사용할 수 있는 애플리케이션 파트 및 기능 공급자를 추적합니다. `ApplicationPartManager`는 `Startup.ConfigureServices`에 구성되어 있습니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

다음 코드는 `AssemblyPart`를 사용하여 `ApplicationPartManager` 구성에 대한 대체 방법을 제공합니다.

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

위의 두 코드 샘플은 어셈블리로부터 `SharedController`를 로드합니다. `SharedController`는 애플리케이션의 프로젝트에 포함되지 않습니다. [WebAppParts 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 샘플 다운로드를 참조하세요.

### <a name="include-views"></a>보기 포함

[ Razor 클래스 라이브러리](xref:razor-pages/ui-class) 를 사용 하 여 어셈블리에 뷰를 포함 합니다.

### <a name="prevent-loading-resources"></a>리소스 로드 방지

애플리케이션 파트를 사용하여 리소스가 특정 어셈블리나 위치에서 로드되는 것을 *방지* 할 수 있습니다. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 컬렉션의 멤버를 추가 또는 제거하여 리소스를 숨기거나 사용 가능하게 만듭니다. `ApplicationParts` 컬렉션에 있는 항목의 순서는 중요하지 않습니다. 컨테이너에서 서비스를 구성하는 데 사용하기 전에 `ApplicationPartManager`를 구성합니다. 예를 들어 `AddControllersAsServices`를 호출하기 전에 `ApplicationPartManager`를 구성합니다. `ApplicationParts` 컬렉션에서 `Remove`를 호출하여 리소스를 제거합니다.

다음 코드는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>를 사용하여 앱에서 `MyDependentLibrary`를 제거합니다.[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager`에는 다음을 위한 파트가 포함됩니다.

* 앱의 어셈블리 및 종속 어셈블리.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>애플리케이션 기능 공급자

애플리케이션 기능 공급자는 애플리케이션 파트를 검사하고 해당 파트에 대한 기능을 제공합니다. 다음 ASP.NET Core 기능에는 기본으로 제공되는 기능 공급자가 있습니다.

* [컨트롤러](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [태그 도우미](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [구성 요소 보기](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

기능 공급자는 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>에서 상속됩니다. 여기서 `T`는 기능의 형식입니다. 기능 공급자는 이전에 나열된 모든 기능 형식에 대해 구현할 수 있습니다. `ApplicationPartManager.FeatureProviders`의 기능 공급자 순서는 런타임 동작에 영향을 줄 수 있습니다. 나중에 추가된 공급자는 이전에 추가된 공급자가 수행한 작업에 반응할 수 있습니다.

### <a name="display-available-features"></a>사용 가능 기능 표시

앱에 사용 가능한 기능은 [종속성 주입](../../fundamentals/dependency-injection.md)을 통해 `ApplicationPartManager`를 요청하여 열거할 수 있습니다.

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[샘플 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)는 위의 코드를 사용하여 앱 기능을 표시합니다.

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>애플리케이션 파트에서 검색

HTTP 404 오류는 애플리케이션 파트를 사용하여 개발할 때 일반적이지는 않습니다. 이러한 오류는 일반적으로 애플리케이션 파트를 검색하는 방법에 대한 필수 요구 사항이 없기 때문에 발생합니다. 앱에서 HTTP 404 오류를 반환하는 경우 다음 요구 사항이 충족되었는지 확인합니다.

* `applicationName` 설정은 검색에 사용되는 루트 어셈블리로 설정해야 합니다. 검색에 사용되는 루트 어셈블리는 일반적으로 진입점 어셈블리입니다.
* 루트 어셈블리에는 검색에 사용되는 파트에 대한 참조가 있어야 합니다. 참조는 직접적 또는 전이적일 수 있습니다.
* 루트 어셈블리는 웹 SDK를 참조해야 합니다.
  * ASP.NET Core 프레임워크에는 검색에 사용되는 루트 어셈블리에 특성을 스탬핑하는 사용자 지정 빌드 논리가 있습니다.

::: moniker-end
