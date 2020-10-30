---
title: ASP.NET Core MVC에서 보기
author: ardalis
description: ASP.NET Core MVC에서 보기가 앱의 데이터 프레젠테이션과 사용자 상호 작용을 처리하는 방식에 대해 알아봅니다.
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
uid: mvc/views/overview
ms.openlocfilehash: 373b17377740441d3859e3b7d942017a22bc7a68
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060627"
---
# <a name="views-in-aspnet-core-mvc"></a>ASP.NET Core MVC에서 보기

작성자: [Steve Smith](https://ardalis.com/)

이 항목에서는 ASP.NET Core MVC 애플리케이션에서 사용되는 보기에 대해 설명합니다. 페이지에 대 한 자세한 내용은 Razor [ Razor 페이지 소개](xref:razor-pages/index)를 참조 하세요.

MVC(Model-View-Controller) 패턴에서 *보기* 는 앱의 데이터 프레젠테이션과 사용자 상호 작용을 처리합니다. 뷰는 포함 된 [ Razor 태그가](xref:mvc/views/razor)있는 HTML 템플릿입니다. Razor 태그는 HTML 태그와 상호 작용 하 여 클라이언트에 전송 되는 웹 페이지를 생성 하는 코드입니다.

MVC ASP.NET Core 뷰는 태그에서 [c # 프로그래밍 언어](/dotnet/csharp/) 를 사용 하는 *. cshtml* 파일입니다. Razor 일반적으로 보기 파일은 앱의 [컨트롤러](xref:mvc/controllers/actions) 각각에 대해 명명된 폴더로 그룹화됩니다. 이 폴더는 앱 루트의 *Views* 폴더에 저장됩니다.

![Visual Studio의 솔루션 탐색기에 About.cshtml, Contact.cshtml 및 Index.cshtml 파일을 표시하도록 Home 폴더가 열린 상태로 Views 폴더가 열려 있습니다.](overview/_static/views_solution_explorer.png)

*Home* 컨트롤러는 *Views* 폴더 내에 *Home* 폴더로 표시됩니다. *Home* 폴더는 *About* , *Contact* 및 *Index* (홈페이지) 웹 페이지에 대한 보기를 포함하고 있습니다. 사용자가 이러한 세 웹 페이지 중 하나를 요청하면 *Home* 컨트롤러의 컨트롤러 작업이 세 가지 보기 중 어떤 보기를 사용하여 웹 페이지를 만들고 사용자에게 반환할지 결정합니다.

일관된 웹 페이지 섹션을 제공하고 코드 반복을 줄이려면 [레이아웃](xref:mvc/views/layout)을 사용하세요. 레이아웃에는 보통 머리글, 탐색 및 메뉴 요소 및 바닥글이 포함됩니다. 머리글 및 바닥글에는 일반적으로 다양한 메타데이터 요소에 대한 상용구 태그와 스크립트 및 스타일 자산에 대한 링크가 포함됩니다. 레이아웃을 사용하면 이런 상용구 태그가 보기에 포함되는 것을 피할 수 있습니다.

[부분 보기](xref:mvc/views/partial)는 재사용 가능한 보기 부분을 관리하여 코드 중복을 줄입니다. 예를 들어, 부분 보기는 여러 보기에 나타나는 블로그 웹 사이트의 작성자 약력에 유용합니다. 작성자 약력은 평범한 보기 콘텐츠이고 웹 페이지의 콘텐츠를 생성하기 위해 코드를 실행하지 않아도 됩니다. 작성자 약력 콘텐츠는 모델 바인딩만으로 보기에서 사용할 수 있으므로 이러한 콘텐츠 유형에는 부분 보기를 사용하는 것이 좋습니다.

[보기 구성 요소](xref:mvc/views/view-components)는 반복 코드를 줄일 수 있다는 점에서 부분 보기와 유사하지만 웹 페이지를 렌더링하기 위해 서버에서 코드를 실행해야 하는 보기 콘텐츠에 적합합니다. 보기 구성 요소는 장바구니와 같이 렌더링된 콘텐츠가 웹 사이트 데이터베이스 상호 작용을 필요로 할 때 유용합니다. 보기 구성 요소는 웹 페이지 출력을 생성하기 위해 모델 바인딩에만 제한되지 않습니다.

## <a name="benefits-of-using-views"></a>뷰 사용 시 이점

보기를 통해 사용자 인터페이스 태그를 앱의 다른 부분과 분리하여 MVC 앱 내에서 [문제의 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)를 확립할 수 있습니다. SoC 디자인을 따르면 앱을 모듈화할 수 있으며 다음과 같은 여러 이점이 제공됩니다.

* 앱이 더 잘 구성되어 있기 때문에 유지 관리가 더 쉽습니다. 보기는 일반적으로 앱 기능별로 그룹화됩니다. 이렇게 하면 기능을 사용할 때 관련된 보기를 보다 쉽게 찾을 수 있습니다.
* 앱의 부분들이 느슨하게 결합됩니다. 비즈니스 논리 및 데이터 액세스 구성 요소와 별도로 앱의 보기를 작성하고 수정할 수 있습니다. 앱의 다른 부분을 수정하지 않고도 앱의 보기를 수정할 수 있습니다.
* 뷰는 별도의 단위이므로 앱의 사용자 인터페이스 부분을 보다 쉽게 테스트할 수 있습니다.
* 구성이 개선되어 사용자 인터페이스 섹션을 실수로 반복하는 일이 줄어듭니다.

## <a name="creating-a-view"></a>뷰 만들기

컨트롤러에 관한 뷰는 *Views/[ControllerName]* 폴더에 생성됩니다. 컨트롤러 간에 공유되는 뷰는 *Views/Shared* 폴더에 배치됩니다. 뷰를 만들려면 새 파일을 추가하고 연결된 컨트롤러 작업과 동일한 이름을 *.cshtml* 파일 확장명으로 지정합니다. *Home* 컨트롤러에서 *About* 작업에 해당하는 뷰를 만들려면 *Views/Home* 폴더에 *About.cshtml* 파일을 만듭니다.

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* 태그는 기호로 시작 `@` 합니다. C # 코드를 중괄호 ()로 설정 된 [ Razor 코드 블록](xref:mvc/views/razor#razor-code-blocks) 내에 배치 하 여 c # 문을 실행 `{ ... }` 합니다. 예를 들어 위에 표시된 `ViewData["Title"]`에 "About"을 할당하는 부분을 참조하세요. `@` 기호로 값을 참조하기만 하면 HTML 내에서 이 값을 표시할 수 있습니다. 위의 `<h2>` 및 `<h3>` 요소의 내용을 참조하세요.

위에 표시된 보기 콘텐츠는 사용자에게 렌더링되는 전체 웹 페이지의 일부분일 뿐입니다. 페이지 레이아웃의 나머지 부분 및 보기의 다른 일반적인 측면은 다른 보기 파일에 지정됩니다. 자세한 내용은 [레이아웃 항목](xref:mvc/views/layout)을 참조하세요.

## <a name="how-controllers-specify-views"></a>컨트롤러에서 보기를 지정하는 방법

일반적으로 보기는 작업에서 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) 형식인 [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult)로 반환됩니다. 작업 메서드에서 직접 `ViewResult`를 만들고 반환할 수도 있지만 일반적으로 그렇게 하지는 않습니다. 대부분의 컨트롤러는 [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller)를 상속하므로 `View` 도우미 메서드를 사용하여 `ViewResult`를 반환합니다.

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

이 작업이 반환되면 마지막 섹션에 표시된 *About.cshtml* 보기가 다음 웹 페이지와 같이 렌더링됩니다.

![Microsoft Edge 브라우저에서 렌더링된 About 페이지](overview/_static/about-page.png)

`View` 도우미 메서드는 몇 가지 오버로드를 갖고 있습니다. 필요에 따라 다음을 지정할 수 있습니다.

* 반환할 명시적 보기:

  ```csharp
  return View("Orders");
  ```

* 보기에 전달할 [모델](xref:mvc/models/model-binding):

  ```csharp
  return View(Orders);
  ```

* 보기 및 모델 모두:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>보기 검색

작업이 보기를 반환하면 *보기 검색* 이라는 프로세스가 수행됩니다. 이 프로세스는 보기 이름을 기반으로 어떤 보기 파일을 사용할지 결정합니다. 

`View` 메서드(`return View();`)의 기본 동작은 호출된 작업 메서드와 같은 이름의 보기를 반환하는 것입니다. 예를 들어 컨트롤러의 *About* `ActionResult` 메서드 이름은 *About.cshtml* 이라는 이름의 보기 파일을 검색하는 데 사용됩니다. 먼저, 런타임은 *Views/[ControllerName]* 폴더에서 보기를 찾습니다. 이 위치에서 일치하는 보기를 찾지 못하면 *Shared* 폴더에서 보기를 검색합니다.

`return View();`;`를 사용하여 암시적으로 `ViewResult`를 반환하거나 `return View("<ViewName>");`를 사용하여 명시적으로 보기 이름을 `View` 메서드에 전달하는 것은 문제가 되지 않습니다. 두 경우 모두, 뷰 검색 시 일치하는 뷰 파일을 다음 순서로 검색합니다.

   1. *Views/\[ControllerName]/\[ViewName].cshtml*
   1. *Views/Shared/\[ViewName].cshtml*

보기 이름 대신 보기 파일의 경로를 제공할 수 있습니다. 앱 루트에서 시작하는 절대 경로를 사용하는 경우(필요에 따라 "/" 또는 "~ /"로 시작하는) *.cshtml* 확장명을 지정해야 합니다.

```csharp
return View("Views/Home/About.cshtml");
```

상재 경로를 사용하여 *.cshtml* 확장명 없이 다른 디렉터리에 뷰를 지정할 수도 있습니다. `HomeController` 내에서 상대 경로로 *Manage* 뷰의 *Index* 뷰를 반환할 수 있습니다.

```csharp
return View("../Manage/Index");
```

마찬가지로, "./" 접두사로 현재 컨트롤러 관련 디렉터리를 나타낼 수 있습니다.

```csharp
return View("./About");
```

[부분 보기](xref:mvc/views/partial) 및 [보기 구성 요소](xref:mvc/views/view-components)도 완전히 동일하지는 않지만 비슷한 검색 메커니즘을 사용합니다.

사용자 지정 [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander)를 사용하여 보기가 앱 내부에 위치하는 방식에 대한 기본 규칙을 사용자 지정할 수 있습니다.

보기 검색은 파일 이름으로 보기 파일을 찾는 방식을 사용합니다. 기본 파일 시스템이 대/소문자를 구분하는 경우 보기 이름도 대/소문자를 구분합니다. 운영 체제 간 호환성을 위해, 컨트롤러 및 작업 이름, 관련 보기 폴더 및 파일 이름 간에 대/소문자를 일치시키세요. 대/소문자 구분 파일 시스템으로 작업하는 동안 파일 보기를 찾지 못하는 오류가 발생하면, 요청된 보기 파일과 실제 보기 파일 이름 간에 대/소문자가 일치하는지 확인합니다.

유지 관리 및 명확성을 위해 컨트롤러, 작업 및 보기 간의 관계를 반영하도록 보기에 대한 파일 구조를 구성하는 것이 가장 좋습니다.

## <a name="passing-data-to-views"></a>뷰에 데이터 전달

여러 가지 방법으로 뷰에 데이터를 전달합니다.

* 강력한 형식의 데이터: viewmodel
* 약한 형식의 데이터
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>강력한 형식의 데이터(viewmodel)

가장 강력한 방법은 뷰에서 [모델](xref:mvc/models/model-binding) 형식을 지정하는 것입니다. 이 모델은 일반적으로 *viewmodel* 이라고 합니다. 작업에서 viewmodel 형식의 인스턴스를 보기에 전달합니다.

viewmodel을 사용하여 뷰에 데이터를 전달하면 뷰에서 *강력한* 형식 검사를 활용할 수 있습니다. *강력한 형식화* (또는 *강력한 형식의* )는 모든 변수 및 상수가 명시적으로 정의된 형식(예: `string`, `int` 또는 `DateTime`)을 포함함을 의미합니다. 뷰에 사용된 형식의 유효성 검사는 컴파일 시간에 검사됩니다.

[Visual Studio](https://visualstudio.microsoft.com) 및 [Visual Studio Code](https://code.visualstudio.com/)는 [IntelliSense](/visualstudio/ide/using-intellisense)라는 기능을 사용하여 강력한 형식의 클래스 멤버를 나열합니다. viewmodel 속성을 보려는 경우 viewmodel에 대한 변수 이름과 마침표(`.`)를 입력합니다. 이렇게 하면 오류를 줄이면서 보다 빠르게 코드를 작성할 수 있습니다.

`@model` 지시문을 사용하여 모델을 지정합니다. `@Model`로 모델을 사용합니다.

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

모델을 보기에 제공하기 위해 컨트롤러는 이를 매개 변수로 전달합니다.

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

뷰에 제공할 수 있는 모델 유형에 대한 제한은 없습니다. 동작(메서드)가 거의 또는 전혀 정의되지 않은 POCO(Plain Old CLR Object) viewmodel을 사용하는 것이 좋습니다. 일반적으로 viewmodel 클래스는 앱의 루트에서 *Models* 폴더 또는 별도의 *ViewModels* 폴더에 저장됩니다. 위의 예제에 사용된 *Address* viewmodel은 *Address.cs* 라는 파일에 저장된 POCO viewmodel입니다.

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Viewmodel 형식 및 비즈니스 모델 형식 모두에 같은 클래스를 사용할 수 있습니다. 그러나 별도의 모델을 사용하면 보기를 통해 앱의 비즈니스 논리 및 데이터 액세스 부분을 독립적으로 구분할 수 있습니다. 모델과 viewmodel을 분리하면 모델에서 사용자에 의해 앱에 전송된 데이터에 대해 [모델 바인딩](xref:mvc/models/model-binding) 및 [유효성 검사](xref:mvc/models/validation)를 사용할 때 보안상 이점도 제공합니다.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>약한 형식의 데이터(ViewData, ViewData 특성 및 ViewBag)

`ViewBag`*에서 Razor 사용할 수 없음 페이지.*

강력한 형식의 뷰 외에도, 뷰는 *약한 형식* ( *느슨한 형식* 이라고도 함) 데이터 컬렉션에 액세스할 수 있습니다. 강력한 형식과 달리, *약한 형식* (또는 *느슨한 형식* )은 사용 중인 데이터 형식을 명시적으로 선언하지 않는 것을 의미합니다. 컨트롤러 및 뷰 간에 적은 양의 데이터를 전달하기 위해 약한 형식의 데이터 컬렉션을 사용할 수 있습니다.

| 다음 사이에 데이터 전달 ...                        | 예제                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| 컨트롤러 및 뷰                             | 드롭다운 목록을 데이터로 채웁니다.                                          |
| 보기 및 [레이아웃 보기](xref:mvc/views/layout)   | **\<title>** 뷰 파일의 레이아웃 뷰에서 요소 콘텐츠를 설정 합니다.  |
| [부분 보기](xref:mvc/views/partial) 및 보기 | 사용자가 요청한 웹 페이지에 따라 데이터를 표시하는 위젯입니다.      |

이 컬렉션은 컨트롤러 및 뷰의 `ViewData` 또는 `ViewBag` 속성을 통해 참조할 수 있습니다. `ViewData` 속성은 약한 형식 개체의 사전입니다. `ViewBag` 속성은 기본 `ViewData` 컬렉션에 대해 동적 속성을 제공하는 `ViewData` 주변의 래퍼입니다. 참고: 키 조회는 및에 대 한 대/소문자를 구분 하지 않습니다 `ViewData` `ViewBag` .

`ViewData` 및 `ViewBag`은 런타임에 동적으로 확인됩니다. 컴파일 시간 형식 검사를 제공하지 않으므로 일반적으로 둘 다 viewmodel을 사용하는 것보다 오류가 발생하기 더 쉽습니다. 이러한 이유로, 일부 개발자는 `ViewData` 및 `ViewBag`을 최소한으로 사용하거나 아예 사용하지 않습니다.

<a name="VD"></a>

**ViewData**

`ViewData`는 `string` 키를 통해 액세스하는 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 개체입니다. 문자열 데이터는 캐스트할 필요 없이 직접 저장 및 사용할 수 있지만 다른 `ViewData` 개체 값을 추출할 때는 특정 형식으로 캐스트해야 합니다. `ViewData`를 사용하여 컨트롤러에서 보기로, [부분 보기](xref:mvc/views/partial) 및 [레이아웃](xref:mvc/views/layout)을 포함한 보기 내에서 데이터를 전달할 수 있습니다.

다음은 작업에서 `ViewData`를 사용하여 인사말 및 주소에 대한 값을 설정하는 예제입니다.

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

뷰에서 데이터 사용:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**ViewData 특성**

[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)를 사용하는 또 다른 방법은 [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)입니다. 특성으로 표시 된 컨트롤러나 페이지 모델의 속성에는 Razor `[ViewData]` 해당 값이 사전에 저장 되 고 로드 됩니다.

다음 예제에서 홈 컨트롤러에는 `[ViewData]`로 표시된 `Title` 속성이 있습니다. `About` 메서드는 정보 보기에 대한 제목을 설정합니다.

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

레이아웃에서는 ViewData 사전으로부터 이 제목을 읽습니다.

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`*에서 Razor 사용할 수 없음 페이지.*

`ViewBag`은 `ViewData`에 저장된 개체에 대한 동적 액세스를 제공하는 [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) 개체입니다. 캐스팅이 필요하지 않으므로 `ViewBag`이 작업하기 더 편리할 수 있습니다. 다음 예제에서는 `ViewData`를 사용할 때와 동일한 결과를 보여주는 `ViewBag`을 사용하는 방법을 보여 줍니다.

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**ViewData 및 ViewBag을 동시에 사용**

`ViewBag`*에서 Razor 사용할 수 없음 페이지.*

`ViewData` 및 `ViewBag`은 동일한 기본 `ViewData` 컬렉션을 사용하므로 `ViewData` 및 `ViewBag`을 모두 사용하고 값을 읽고 쓸 때 이들을 혼합 및 일치시킬 수 있습니다.

*About.cshtml* 보기의 맨 위에서 `ViewBag`을 사용하여 제목을, `ViewData`를 사용하여 설명을 설정합니다.

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

속성을 읽지만 `ViewData` 및 `ViewBag`을 반대로 사용합니다. *_Layout.cshtml* 파일에서는 `ViewData`를 사용하여 제목을 가져오고 `ViewBag`을 사용하여 설명을 가져옵니다.

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

`ViewData`의 문자열은 캐스트가 필요하지 않습니다. 캐스팅없이 `@ViewData["Title"]`를 사용할 수 있습니다.

`ViewData`와 `ViewBag`을 동시에 사용하면 속성을 읽고 쓰는 작업을 혼합 및 일치시킬 수 있습니다. 다음 태그가 렌더링됩니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**ViewData 및 ViewBag 간의 차이점 요약**

 `ViewBag` 페이지에서 사용할 수 없습니다 Razor .

* `ViewData`
  * 는 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)에서 파생 되므로,, 및와 같이 유용할 수 있는 사전 속성이 있습니다 `ContainsKey` `Add` `Remove` `Clear` .
  * 사전의 키는 문자열이므로 공백을 사용할 수 있습니다. 예: `ViewData["Some Key With Whitespace"]`
  * `string` 이외의 모든 형식을 뷰에서 `ViewData`를 사용하도록 캐스트해야 합니다.
* `ViewBag`
  * [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)에서 파생되므로 점 표기법(`@ViewBag.SomeKey = <value or object>`)을 사용하여 동적 속성을 생성할 수 있으며 캐스팅이 필요하지 않습니다. `ViewBag` 구문을 사용하면 신속하게 컨트롤러와 보기에 추가할 수 있습니다.
  * 간단하게 Null 값을 확인합니다. 예: `@ViewBag.Person?.Name`

**ViewData 또는 ViewBag을 사용하는 경우**

`ViewData` 및 `ViewBag` 둘 다 컨트롤러 및 보기 간에 적은 양의 데이터를 전달하는 데 유효한 방법입니다. 어떤 것을 사용할지는 선호도에 따라서 선택하면 됩니다. `ViewData` 및 `ViewBag` 개체를 혼합 및 일치시키실 수는 있지만, 일관된 한 가지 방법을 사용하는 편이 코드를 보다 쉽게 읽고 유지 관리할 수 있습니다. 두 가지 방법 모두 런타임에 동적으로 확인되므로 런타임 오류가 발생하기 쉽습니다. 일부 개발 팀은 이 방법을 사용하지 않습니다.

### <a name="dynamic-views"></a>동적 보기

`@model`을 사용하여 모델 형식을 선언하지는 않지만 전달된 모델 인스턴스가 있는 보기(예: `return View(Address);`)는 인스턴스의 속성을 동적으로 참조할 수 있습니다.

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

이 기능은 유연성을 제공하지만 컴파일 보호 또는 IntelliSense는 제공하지 않습니다. 속성이 존재하지 않으면 런타임 시 웹 페이지 생성에 실패합니다.

## <a name="more-view-features"></a>더 많은 보기 기능

[태그 도우미](xref:mvc/views/tag-helpers/intro)를 통해 기존 HTML 태그에 서버 쪽 동작을 쉽게 추가할 수 있습니다. 태그 도우미를 사용하면 보기 내에서 사용자 지정 코드 또는 도우미를 작성할 필요가 없습니다. 태그 도우미는 HTML 요소에 특성으로 적용되고 처리할 수 없는 편집기에 의해 무시됩니다. 따라서 다양한 도구로 보기 태그를 편집 및 렌더링할 수 있습니다.

많은 기본 제공 HTML 도우미를 통해 사용자 지정 HTML 태그를 생성할 수 있습니다. 더 복잡한 사용자 인터페이스 논리는 [보기 구성 요소](xref:mvc/views/view-components)로 처리할 수 있습니다. 보기 구성 요소는 컨트롤러 및 보기에서 제공하는 것과 동일한 SoC를 제공합니다. 공통적인 사용자 인터페이스 요소에서 사용되는 데이터를 처리하는 작업 및 보기에 대한 필요성을 제거할 수 있습니다.

ASP.NET Core의 다른 많은 요소와 마찬가지로 보기는 [종속성 주입](xref:fundamentals/dependency-injection)을 지원하며, [보기에 서비스를 주입](xref:mvc/views/dependency-injection)할 수 있습니다.
