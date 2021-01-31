---
title: ASP.NET Core에서 애플리케이션 모델 작업
author: ardalis
description: MVC 요소가 ASP.NET Core에서 작동하는 방법을 수정하려면 애플리케이션 모델을 읽고 조작하는 방법을 알아봅니다.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/application-model
ms.openlocfilehash: cf16536284ee9c88913257607df837ad6e50ea2c
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217377"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>ASP.NET Core에서 애플리케이션 모델 작업

작성자: [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC는 MVC 앱의 구성 요소를 나타내는 *응용 프로그램 모델* 을 정의합니다. 이 모델을 읽고 조작하여 MVC 요소의 작동 방법을 수정할 수 있습니다. 기본적으로 MVC는 어떤 클래스가 컨트롤러로 간주되는지, 해당 클래스의 어떤 메서드가 작업인지 및 매개 변수와 라우팅의 작동 방법을 결정하는 특정 규칙을 따릅니다. 고유한 규칙을 만들고 전역으로 또는 특성으로 적용하여 앱의 요구 사항에 맞게 이 동작을 사용자 지정할 수 있습니다.

## <a name="models-and-providers"></a>모델 및 공급자

ASP.NET Core MVC 응용 프로그램 모델에는 추상 인터페이스와 MVC 응용 프로그램을 설명 하는 구체적 구현 클래스가 모두 포함 됩니다. 이 모델은 기본 규칙에 따라 MVC가 앱의 컨트롤러, 작업, 작업 매개 변수, 경로 및 필터를 검색한 결과입니다. 애플리케이션 모델을 사용하여 기본 MVC 동작과 다른 규칙을 따르도록 앱을 수정할 수 있습니다. 매개 변수, 이름, 경로 및 필터는 모두 작업 및 컨트롤러에 대한 구성 데이터로 사용됩니다.

ASP.NET Core MVC 응용 프로그램 모델의 구조는 다음과 같습니다.

* ApplicationModel
  * 컨트롤러(ControllerModel)
    * 작업(ActionModel)
      * 매개 변수(ParameterModel)

모델의 각 수준은 공통 `Properties` 컬렉션에 액세스할 수 있으며, 하위 수준은 계층의 상위 수준에서 설정된 속성 값에 액세스하고 덮어쓸 수 있습니다. 이 속성들은 작업이 만들어질 때 `ActionDescriptor.Properties`에 유지됩니다. 그런 다음 요청을 처리할 때 규칙에서 추가하거나 수정한 모든 속성을 `ActionContext.ActionDescriptor.Properties`를 통해서 액세스할 수 있습니다. 작업별로 속성을 사용하여 필터, 모델 바인더 등을 구성하는 것도 좋은 방법입니다.

> [!NOTE]
> 일단 앱 시작이 완료되고 나면 `ActionDescriptor.Properties` 컬렉션은 스레드에 대해 안전하지 않습니다(쓰기에 대해). 이 컬렉션에 데이터를 안전하게 추가하는 가장 좋은 방법은 규칙입니다.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC는 [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) 인터페이스에 의해 정의된 공급자 패턴을 사용하여 응용 프로그램 모델을 로드합니다. 이 섹션에서는 해당 공급자가 작동하는 방법에 대한 일부 내부 구현 세부 사항을 다룹니다. 이는 규칙을 사용하여 애플리케이션 모델을 활용하는 대부분의 앱이 수행해야 하는 고급 항목입니다.

`IApplicationModelProvider` 인터페이스의 구현은 서로 "래핑"하며, 각 구현은 해당 `Order` 속성에 따라 오름차순으로 `OnProvidersExecuting`을 호출합니다. `OnProvidersExecuted` 메서드는 역순으로 호출됩니다. 프레임워크는 여러 공급자를 정의합니다.

First(`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Then(`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> `Order`에 대해 같은 값을 가진 두 공급자를 호출하는 순서는 정의되지 않았으며 따라서 사용하지 말아야 합니다.

> [!NOTE]
> `IApplicationModelProvider`는 프레임워크 확장 작성자를 위한 고급 개념입니다. 일반적으로 앱은 규칙을 사용해야 하고 프레임워크는 공급자를 사용해야 합니다. 중요한 차이점은 공급자가 항상 규칙보다 먼저 실행된다는 점입니다.

`DefaultApplicationModelProvider`는 ASP.NET Core MVC가 사용하는 대부분의 기본 동작을 설정합니다. 이 공급자의 책임은 다음과 같습니다.

* 컨텍스트에 전역 필터 추가
* 컨텍스트에 컨트롤러 추가
* 공용 컨트롤러 메서드를 작업으로 추가
* 컨텍스트에 작업 메서드 매개 변수 추가
* 경로 및 기타 특성 적용

일부 기본 제공 동작은 `DefaultApplicationModelProvider`에 의해 구현됩니다. 이 공급자는을 생성 하 [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) 고, 및 인스턴스를 참조 합니다 [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) . `DefaultApplicationModelProvider` 클래스는 향후 변경될 수 있는 내부 프레임워크 구현 세부 사항입니다. 

`AuthorizationApplicationModelProvider`는 `AuthorizeFilter` 및 `AllowAnonymousFilter` 특성과 관련된 동작의 적용을 담당합니다. [이 특성들에 대해 자세히 알아보세요](xref:security/authorization/simple).

`CorsApplicationModelProvider`는 `IEnableCorsAttribute`, `IDisableCorsAttribute` 및 `DisableCorsAuthorizationFilter`와 관련된 동작을 구현합니다. [CORS에 대해 자세히 알아봅니다](xref:security/cors).

## <a name="conventions"></a>규칙

애플리케이션 모델은 전체 모델 또는 공급자를 재정의하기 보다는 모델의 동작을 사용자 지정하는 간단한 방법을 제공하는 규칙 추상화를 정의합니다. 앱의 동작을 수정할 경우 이러한 추상화를 사용하는 것이 좋습니다. 규칙은 사용자 지정을 동적으로 적용하는 코드를 작성할 수 있는 방법을 제공합니다. [필터](xref:mvc/controllers/filters)는 프레임워크의 동작을 수정하는 방법을 제공하는 반면, 사용자 지정을 통해 전체 앱이 함께 작동하는 방법을 제어할 수 있습니다.

사용할 수 있는 규칙은 다음과 같습니다.

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

규칙은 MVC 옵션에 추가 하거나를 구현 하 `Attribute` 고 컨트롤러, 작업 또는 작업 매개 변수에 적용 하 여 적용 됩니다 (과 유사 [`Filters`](xref:mvc/controllers/filters) ). 필터와 달리 각 요청의 일부가 아니라 앱을 시작하는 경우에만 규칙이 실행됩니다.

### <a name="sample-modifying-the-applicationmodel"></a>샘플: ApplicationModel 수정

다음 규칙은 애플리케이션 모델에 속성을 추가하는 데 사용됩니다. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

`Startup`의 `ConfigureServices`에 MVC를 추가할 때 응용 프로그램 모델 규칙을 옵션으로 적용합니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

속성은 컨트롤러 작업 내의 `ActionDescriptor` 속성 컬렉션에서 액세스할 수 있습니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>샘플: ControllerModel 설명 수정

이전의 예제처럼 컨트롤러 모델은 사용자 지정 속성을 포함하도록 수정될 수도 있습니다. 그러면 애플리케이션 모델에 지정된 동일한 이름을 가진 기존 속성을 덮어씁니다. 다음 규칙 특성은 컨트롤러 수준에서 설명을 추가합니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

이 규칙은 컨트롤러에 대한 특성으로 적용됩니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

이전 예제와 같이 동일한 방식으로 "설명" 속성에 액세스합니다.

### <a name="sample-modifying-the-actionmodel-description"></a>샘플: ActionModel 설명 수정

개별 작업에 별도의 특성 규칙을 적용할 수 있습니다. 그러면 이미 애플리케이션 또는 컨트롤러 수준에서 적용되는 동작을 재정의합니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

이전 예제의 컨트롤러 내에 있는 작업에 이를 적용하면 컨트롤러 수준 규칙을 재정의하는 방법을 보여줍니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>샘플: ParameterModel 수정

`BindingInfo`를 수정하는 작업 매개 변수에 다음 규칙을 적용할 수 있습니다. 다음과 같은 규칙의 매개 변수는 경로 매개 변수여야 합니다. 다른 잠재적인 바인딩 소스(예: 쿼리 문자열 값)는 무시됩니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

특성이 모든 작업 매개 변수에 적용될 수 있습니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>샘플: ActionModel 이름 수정

다음 규칙은 적용되는 작업의 *이름* 을 업데이트하도록 `ActionModel`을 수정합니다. 새 이름은 특성에 대한 매개 변수로 제공됩니다. 라우팅에서 새 이름을 사용하므로 이 작업 메서드에 연결하는 데 사용되는 경로에 영향을 줍니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

해당 특성이 `HomeController`의 작업 메서드에 적용되었습니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

메서드 이름은 `SomeName`이지만 이 특성이 메서드 이름을 사용하는 MVC 규칙을 재정의하여 작업 이름을 `MyCoolAction`으로 변경합니다. 따라서 이 작업에 도달하는 데 사용되는 경로는 `/Home/MyCoolAction`입니다.

> [!NOTE]
> 이 예제는 기본적으로 기본 제공 [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) 특성을 사용하는 것과 같습니다.

### <a name="sample-custom-routing-convention"></a>샘플: 사용자 지정 라우팅 규칙

`IApplicationModelConvention`을 사용하여 라우팅 작동 방법을 사용자 지정할 수 있습니다. 예를 들어 다음 규칙은 컨트롤러의 네임스페이스를 해당 경로에 통합하고 네임스페이스의 `.`를 경로의 `/`로 바꿉니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

이 규칙은 Startup에서 옵션으로 추가됩니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`를 사용해서 `MvcOptions`에 액세스하여 [미들웨어](xref:fundamentals/middleware/index)에 규칙을 추가할 수 있습니다.

이 예제에서는 컨트롤러의 이름에 "Namespace"가 포함된 특성 라우팅을 사용하지 않는 경로를 대상으로 규칙을 적용합니다. 다음 컨트롤러는 이 규칙을 보여줍니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>WebApiCompatShim의 응용 프로그램 모델 사용

ASP.NET Core MVC는 ASP.NET Web API 2와 다른 규칙 집합을 사용합니다. 사용자 지정 규칙을 사용하면 ASP.NET Core MVC 앱의 동작을 Web API 앱의 동작과 일치하도록 수정할 수 있습니다. Microsoft는 이를 위해 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/)을 제공합니다.

> [!NOTE]
> [ASP.NET Web API에서 마이그레이션](xref:migration/webapi)에 대해 자세히 알아봅니다.

Web API 호환성 Shim을 사용하려면 프로젝트에 패키지를 추가한 다음, `Startup`에서 `AddWebApiConventions`를 호출하여 MVC에 규칙을 추가해야 합니다.

```csharp
services.AddMvc().AddWebApiConventions();
```

shim에서 제공하는 규칙은 특정 특성이 적용된 앱의 일부에만 적용됩니다. 다음 네 가지 특성이 shim의 규칙에 따라 규칙을 수정해야 하는 컨트롤러를 제어하는 데 사용됩니다.

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>작업 규칙

`UseWebApiActionConventionsAttribute`는 작업 이름을 기준으로 HTTP 메서드를 작업에 매핑하는 데 사용됩니다(예: `Get`은 `HttpGet`에 매핑됨). 특성 라우팅을 사용하지 않는 작업에만 적용됩니다.

### <a name="overloading"></a>오버로딩

`UseWebApiOverloadingAttribute`는 `WebApiOverloadingApplicationModelConvention` 규칙을 적용하는 데 사용됩니다. 이 규칙은 작업 선택 프로세스에 `OverloadActionConstraint`를 추가하는데, 이는 후보 작업을 요청이 모든 비 선택적 매개 변수를 충족시키는 작업으로 제한합니다.

### <a name="parameter-conventions"></a>매개 변수 규칙

`UseWebApiParameterConventionsAttribute`는 `WebApiParameterConventionsApplicationModelConvention` 작업 규칙을 적용하는 데 사용됩니다. 이 규칙은 작업 매개 변수로 사용된 단순 형식은 기본적으로 URI에서 바인드되는 반면 복합 형식은 요청 본문에서 바인드되도록 지정합니다.

### <a name="routes"></a>경로

`UseWebApiRoutesAttribute`는 `WebApiApplicationModelConvention` 컨트롤러 규칙을 적용할지 여부를 제어합니다. 이 규칙을 사용하면 경로에 [영역](xref:mvc/controllers/areas)에 대한 지원을 추가하는 데 사용됩니다.

호환성 패키지에는 규칙 집합 외에도 Web API에서 제공하는 클래스를 대신하는 `System.Web.Http.ApiController` 기본 클래스가 포함되어 있습니다. 이를 사용하면 Web API용으로 작성되고 Web API의 `ApiController`에서 상속된 컨트롤러를 ASP.NET Core MVC에서 실행하더라도 설계한 바대로 작동시킬 수 있습니다. 앞서 나열된 모든 `UseWebApi*` 특성은 기본 컨트롤러 클래스에 적용됩니다. `ApiController`는 Web API에서 찾을 수 있는 것과 호환되는 속성, 메서드 및 결과 형식을 노출합니다.

## <a name="using-apiexplorer-to-document-your-app"></a>ApiExplorer를 사용하여 앱 문서화

응용 프로그램 모델은 응용 프로그램 [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) 의 구조를 트래버스하는 데 사용할 수 있는 속성을 각 수준에서 노출 합니다. 이를 [Swagger와 같은 도구를 사용하여 Web API용 도움말 페이지를 생성](xref:tutorials/web-api-help-pages-using-swagger)하는 데 사용할 수 있습니다. `ApiExplorer` 속성은 앱의 모델이 노출해야 하는 부분을 지정하도록 설정할 수 있는 `IsVisible` 속성을 노출합니다. 규칙을 사용하여 이 설정을 구성할 수 있습니다.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

이 접근 방식(및 필요한 경우 추가 규칙)을 사용하여 앱 내의 모든 수준에서 API 가시성을 사용하거나 사용하지 않을 수 있습니다. 
